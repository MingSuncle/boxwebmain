<template>
    <el-menu :default-openeds="['2']" style="width:210px;min-height:100%;overflow-x: hidden;" background-color="rgb(48,65,86)"
        text-color='#fff' active-text-color="#2990d6" :collapse-transition="true" :collapse="isCollapse" router>
        <div style="height:60px;line-height: 60px;text-align: center;">
            <!-- <img src="../assets/logoa.png" alt="" style="width:20px;position:relative;top:5px;margin-right: 5px;"> -->
            <i class="el-icon-set-up" style="color:#fff"></i>
            <b style="color:white " v-show="logoTextShow">智能设备管理平台</b>
        </div>
        <el-menu-item index="/">
            <template slot="title">
            <i class="el-icon-house"></i>
            <span slot="title">主页</span>
        </template>
        </el-menu-item>
        <el-menu-item index="/event">
        <i class="el-icon-s-platform"></i>
        <span slot="title">事件管理</span>
        </el-menu-item>
        <!-- <el-menu-item index="/user">
        <i class="el-icon-user-solid"></i>
        <span slot="title">用户管理</span>
        </el-menu-item>
        <el-menu-item index="/dataManage">
        <i class="el-icon-cloudy"></i>
        <span slot="title">数据管理</span>
      </el-menu-item> -->
    </el-menu>
</template>

<script>
    export default{
        name:"Aside",
        props:{
            isCollapse:Boolean,
            logoTextShow:Boolean
        }
    }
</script>

<style scoped>
</style>