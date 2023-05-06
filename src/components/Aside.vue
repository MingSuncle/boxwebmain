<template>
    <div>
        <div class="menuplaceholder"></div>
        <el-menu class="menu" :default-openeds="['2']" background-color="rgb(48,65,86)" text-color='#fff'
            active-text-color="#2990d6" :collapse-transition="true" :collapse="isCollapse" router>
            <div style="height:60px;line-height: 60px;text-align: center;">
                <!-- <img src="../assets/logoc.png" alt="" style="width:170px;position:relative;top:5px;margin-right: 5px;"> -->
                <b style="color:white " v-show="logoTextShow">智能平台(demo)</b>
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
    </div>
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
.menuplaceholder {
    width: 200px;
}

.menu {
    position: fixed;
    left: 0;
    bottom: 0;
    width: 100%;
    z-index: 1000;
    top: 0px;
    /* 菜单组件的顶部偏移量等于占位元素的高度 */
    left: 0;
    width: 200px;
}

.sidebar {
    display: block;
    position: absolute;
    left: 0;
    top: 70px;
    bottom: 0;
    overflow-y: scroll;
}

.sidebar::-webkit-scrollbar {
    width: 0;
}

.sidebar-el-menu:not(.el-menu--collapse) {
    width: 200px;
}

.sidebar>ul {
    height: 100%;
}
</style>
</style>