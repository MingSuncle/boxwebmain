<template>
    <div>
        <b>盒子管理</b>
        <div style="margin:10px 0">
            <el-button type="primary" @click="newBox">新增盒子 <i class="el-icon-circle-plus-outline"></i></el-button>
        </div>


        <el-table :data="tableData" border>
            <el-table-column prop="boxId" label="盒子序列号" align="center">
            </el-table-column>

            <el-table-column prop="aieventLimit" label="事件限制" align="center">
            </el-table-column>
            <el-table-column prop="channelNumberLimit" label="通道限制数" align="center" width="120">
            </el-table-column>
            <el-table-column prop="expireTime" label="过期时间" align="center">
            </el-table-column>
            <el-table-column prop="boxState" label="盒子状态" align="center" width="150">
                <template slot-scope="scope">
                    <el-tag :type="scope.row.boxState == '0'
                            ? 'info'
                            : scope.row.boxState == '1'
                                ? 'success'
                                : scope.row.boxState == '2'
                                    ? 'danger'
                                    : 'info'
                        " disable-transitions>{{
        scope.row.boxState == '0'
        ? '未激活'
        : scope.row.boxState == '1'
            ? '已激活'
            : scope.row.boxState == '2' ?
                '已锁定'
                : '无法识别'
    }}</el-tag>
                </template>
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
                :current-page="current_page" :page-sizes="[5, 10, 15, 20]" :page-size="pageSize"
                layout="total, sizes, prev, pager, next, jumper" :total="total">
            </el-pagination>
        </div>

        <el-dialog title="新增盒子配置" :visible.sync="createVisible" width="40%">
            <el-form :model="editForm" label-width="120px" ref="editFormRule" :rules="rules">
                <el-form-item label="盒子ID" prop="boxId"><el-input v-model="editForm.boxId"
                        placeholder="请输入模型ID"></el-input></el-form-item>
                <el-form-item label="允许最大通道数" prop="channelNumberLimit"><el-input placeholder="请输入允许最大通道数"
                        v-model.number="editForm.channelNumberLimit"></el-input></el-form-item>
                <el-form-item label="过期时间" prop="expireTime">
                    <el-date-picker v-model="editForm.expireTime" :picker-options="pickerOptions" placeholder="选择过期时间"
                        value-format="yyyy-MM-dd">
                    </el-date-picker>
                </el-form-item>
                <el-form-item label="搭载事件" prop="aieventLimit">
                    <el-select v-model="editForm.aieventLimit" multiple placeholder="请选择">
                        <el-option v-for="item in event" :key="item.key" :label="item.value" :value="item.key">
                        </el-option>
                    </el-select>
                </el-form-item>
            </el-form><span slot="footer" class="dialog-footer">
                <el-button @click="cancelCreate">取 消</el-button>
                <el-button type="primary" @click="handleCreate(editForm)">确 定</el-button>
            </span>
        </el-dialog>

        <el-dialog title="修改盒子配置" :visible.sync="editVisible" width="40%">
            <el-form :model="editForm" label-width="120px" ref="editFormRule" :rules="rules">
                <el-form-item label="盒子ID" prop="boxId">
                    <p>{{editForm.boxId}}</p>
                </el-form-item>
                <el-form-item label="允许最大通道数" prop="channelNumberLimit"><el-input placeholder="请输入允许最大通道数"
                        v-model.number="editForm.channelNumberLimit"></el-input></el-form-item>
                <el-form-item label="过期时间" prop="expireTime">
                    <el-date-picker v-model="editForm.expireTime" :picker-options="pickerOptions" placeholder="选择过期时间"
                        value-format="yyyy-MM-dd">
                    </el-date-picker>
                </el-form-item>
                <el-form-item label="搭载事件" prop="aieventLimit">
                    <el-select v-model="aieventLimit" multiple placeholder="请选择">
                        <el-option v-for="item in event" :key="item.key" :label="item.value" :value="item.key">
                        </el-option>
                    </el-select>
                </el-form-item>
                <el-form-item label="盒子状态" prop="boxState">
                    <el-select v-model="editForm.boxState" placeholder="请选择">
                        <el-option v-for="item in states" :key="item.value" :label="item.label" :value="item.value">
                            <span>
                                <el-tag :type="item.value == '0'
                                                    ? 'info'
                                                    : item.value == '1'
                                                        ? 'success'
                                                        : item.value == '2'
                                                            ? 'danger'
                                                            : 'info'
                                                " disable-transitions>{{
                    item.value == '0'
                    ? '未激活'
                    : item.value == '1'
                        ? '已激活'
                        : item.value == '2' ?
                            '已锁定'
                            : '无法识别'
                }}</el-tag>
                            </span>
                        </el-option>
                    </el-select>
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

export default {
    name: "User",
    data() {
        return {
            // cid: JSON.parse(localStorage.getItem("user")).cid,
            total: 0,
            pageSize: 10,
            current_page: 1,
            tableData: [],
            phone: "",
            form: {},
            aieventLimit: [],
            editForm: {
                aieventLimit: []
            },
            datatest: {},
            value: '',
            event: [],
            createVisible: false,
            editVisible: false,
            states: [
                {
                    value: 0,
                    label: "未激活"
                },
                {
                    value: 1,
                    label: "已激活"
                },
                {
                    value: 2,
                    label: "已锁定"
                }
            ],
            rules: {
                boxId: [
                    {
                        required: true, message: '请输入盒子ID', trigger: 'blur'
                    },
                ],
                channelNumberLimit: [
                    {
                        required: true, message: '请输入允许最大通道数', trigger: 'blur'
                    },
                    {
                        type: 'number', message: '必须为数字'
                    }
                ]
            },
            pickerOptions: {
                disabledDate(time) {
                    return time.getTime() < Date.now();
                }
            }
        }
    },
    props: {

    },
    created() {
        this.load()
    },
    computed: {
        isValid() {
            return !isNaN(parseFloat(this.editForm.channelNumberLimit));
        },
    },
    methods: {
        newBox() {
            this.createVisible = true;
        },
        search() {

        },
        handleEdit(row) {
            this.editForm.aieventLimit = this.aieventLimit.join();
            this.request.post("/box/editBox",this.editForm).then(res => {
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
            this.aieventLimit = this.editForm.aieventLimit.split(',')
            console.log(this.aieventLimit)
            console.log(this.editForm)
            this.editVisible = true;
        },

        handleDelete(index, row) {
            this.$confirm('确认删除盒子？', '提示', {
                type: 'warning',
                confirmButtonText: '确定',
                cancelButtonTest: '取消'
            }).then(() => (
                this.request.get("/box/deleteBox", {
                    params: {
                        box_id: row.boxId,
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
            this.$refs.editFormRule.validate((valid) => {
                if (valid) {
                    this.editForm.boxState = 0;
                    this.editForm.aieventLimit = this.editForm.aieventLimit.join(',')

                    this.request.post("/box/addBox", this.editForm).then(res => {
                        if (res.code == 200) {
                            this.$message({
                                message: '添加成功',
                                type: 'success'
                            });
                            this.load();
                            this.createVisible = false
                        }
                        else if (res.code == 300) {
                            this.$message.error('盒子ID已存在！');
                            this.editForm = {}
                            console.log(this.editForm)
                        }
                        else {
                            this.$message.error('添加失败')
                            this.editForm = Object.assign({}, this.editForm)
                        }
                    })
                } else {
                    this.$message.error('请规范输入！');
                    return false;
                }
            });

        },
        load() {
            this.event = [],
            this.request.get("/box/getEvent").then(res => {
                this.datatest = res.data.result
                const values = Object.values(this.datatest);
                const keys = Object.keys(this.datatest);
                const keysArr = Array.from(keys);
                const valuesArr = Array.from(values);
                const objArr = [];
                for (let i = 0; i < keysArr.length; i++) {
                    this.event.push({ key: keysArr[i], value: valuesArr[i] });
                }
            })
            this.request.get("/box/getBox", {
                params: {
                    current_page: this.current_page,
                    page_size: this.pageSize,

                }
            }).then(res => {
                this.tableData = res.data.result
                this.total = res.data.total

            })

        },
        handleSizeChange(pageSize) {
            this.pageSize = pageSize
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