<template>
    <div>
        <b>运维指令管理</b>
        <div style="margin:10px 0">
            <el-button type="primary" @click="newCommand">新增命令 <i class="el-icon-circle-plus-outline"></i></el-button>
        </div>


        <el-table :data="tableData" border>
            <el-table-column prop="commandName" label="指令名称" align="center">
            </el-table-column>
            <el-table-column prop="commandDescription" label="事件描述" align="center">
            </el-table-column>
            <el-table-column prop="commandDetails" label="具体指令" align="center">
            </el-table-column>
            <el-table-column prop="commandCreateTime" label="修改时间" align="center">
            </el-table-column>
            <el-table-column prop="commandCreatorName" label="修改人" align="center">
            </el-table-column>
            <el-table-column label="操作" align="center" min-width="100">
                <template #default="scope">
                    <el-button type="text" icon="el-icon-edit" class="blue"
                        @click="handleEditButton(scope.$index, scope.row)">修改
                    </el-button>
                    <el-button slot="reference" type="text" icon="el-icon-edit" class="blue"
                        @click="handleDelete(scope.$index, scope.row)">删除
                    </el-button>
                </template>
            </el-table-column>
        </el-table>
        <div style="padding:10px 0">
            <el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange"
                :current-page="current_page" :page-sizes="[5, 10, 15, 20]" :page-size="page_size"
                layout="total, sizes, prev, pager, next, jumper" :total="total">
            </el-pagination>
        </div>

        <el-dialog title="新增指令" :visible.sync="createVisible" width="40%">
            <el-form :model="editForm" label-width="120px" ref="editForm" :rules="rules">
                <el-form-item label="指令名称" prop="commandName"><el-input placeholder="请输入指令名称"
                        v-model="editForm.commandName"></el-input></el-form-item>
                <el-form-item label="指令描述" prop="commandDescription">
                    <el-input placeholder="请输入指令描述"
                        v-model="editForm.commandDescription"></el-input>
                </el-form-item>
                <el-form-item label="具体指令" prop="commandDetails">
                    <el-input placeholder="请输入指令内容"
                        v-model="editForm.commandDetails"></el-input>
                </el-form-item>
 
            </el-form><span slot="footer" class="dialog-footer">
                <el-button @click="cancelCreate">取 消</el-button>
                <el-button type="primary" @click="handleCreate(editForm)">确 定</el-button>
            </span>
        </el-dialog>

        <el-dialog title="修改事件" :visible.sync="editVisible" width="40%">
            <el-form :model="editForm" label-width="120px" ref="editForm" :rules="rules">
                <el-form-item label="指令名称" prop="commandName"><el-input placeholder="请输入指令名称"
                        v-model="editForm.commandName"></el-input></el-form-item>
                <el-form-item label="指令描述" prop="commandDescription">
                    <el-input placeholder="请输入指令描述"
                        v-model="editForm.commandDescription"></el-input>
                </el-form-item>
                <el-form-item label="具体指令" prop="commandDetails">
                    <el-input placeholder="请输入指令内容"
                        v-model="editForm.commandDetails"></el-input>
                </el-form-item>
            </el-form><span slot="footer" class="dialog-footer">
                <el-button @click="cancelEdit">取 消</el-button>
                <el-button type="primary" @click="handleEdit(editForm)">确 定</el-button>
            </span>
        </el-dialog>


    </div>
</template>

<script>
import axios from 'axios';
import { METHODS } from 'http';
import moment from 'moment';

export default {
    name: "User",
    data() {
        return {
            // cid: JSON.parse(localStorage.getItem("user")).cid,
            uid : 1,  //之后要记得改
            total: 0,
            page_size: 10,
            current_page: 1,
            tableData: [],
            form: {},
            aieventLimit: [],
            editForm: {},
            value: '',
            rules: {
                commandName: [{ required: true, message: '请输入指令名称', trigger: 'blur' }],
                commandDetails:[{required: true, message: '请输入指令内容', trigger: 'blur'}]
            },
            createVisible: false,
            editVisible: false,


        }
    },
    props: {

    },
    created() {
        this.load()
    },
    computed: {
    },
    methods: {
        newCommand() {
            this.createVisible = true;
        },
        search() {

        },
        handleEdit(row) {
            var now = new moment();
            this.editForm.commandCreateTime = now.format('YYYY-MM-DD HH:mm:ss')
            this.editForm.commandCreator = this.uid
            this.request.post("/command/saveOrUpdateCommand",this.editForm).then(res => {
                if(res.code == 200){
                    this.$message({
                    message: '修改成功',
                    type : 'success'
                    })
                    this.load();
                }
                else{
                this.$message.error('修改失败')
            }
            });
            this.editVisible = false;
            this.editForm = {};

        },
        handleEditButton(index, row) {
            this.editForm = { ...row };
            this.editVisible = true;
        },

        handleDelete(index, row) {
            this.$confirm('确认删除运维命令？', '提示', {
                type: 'warning',
                confirmButtonText: '确定',
                cancelButtonTest: '取消'
            }).then(() => (
                this.request.get("/command/deleteCommand", {
                    params: {
                        command_id: row.commandId,
                    }
                }).then(res => {
                    if (res.code == 200) {
                        this.$message({
                            message: '删除成功',
                            type: 'success'
                        });
                        this.load();
                    }
                    else {
                        this.$message.error('删除失败')
                    }
                }))
            );
        },
        cancelEdit() {
            this.editVisible = false;
            this.editForm = {};
            this.$message({
                message: '操作已取消',
                duration: 1500,
                type: 'info'
            });
        },
        cancelCreate() {
            this.createVisible = false;
            this.editForm = {};
            this.$message({
                message: '操作已取消',
                duration: 1500,
                type: 'info'
            });
        },
        handleCreate(editForm) {
            this.editForm.commandCreator = this.uid
            this.request.post("/command/saveOrUpdateCommand",this.editForm).then(res => {
                if(res.code == 200){
                    this.$message({
                        message:'添加成功',
                        type:'success'
                    });
                    this.editForm = {};
                    this.load();
                    this.createVisible = false
                }
                else{
                    this.$message.error('添加失败')
                    
                }
            })

        },
        load() {

            this.request.get("/command/getAll", {
                params: {
                    current_page: this.current_page,
                    page_size: this.page_size,

                }
            }).then(res => {
                this.tableData = res.data.result
                this.total = res.data.total

            })

        },
        handleSizeChange(page_size) {
            this.page_size = page_size
            this.load()
        },
        handleCurrentChange(current_page) {
            this.current_page = current_page
            this.load()
        },

    }
}
</script>

<style scoped></style>