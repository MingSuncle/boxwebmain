<template>

    <body id="paper">
        <div style="margin-top: 50px; margin-left: 45%;">
            <h1 style="position: center;">盒子管理平台</h1>
        </div>
        <el-form :model="loginForm" :rules="rules" class="login-container" label-position="left" label-width="0px">

            <el-form-item prop="username">
                <el-input type="text" v-model="loginForm.username" auto-complete="off" placeholder="账号"></el-input>
            </el-form-item>
            <el-form-item prop="password">
                <el-input type="password" v-model="loginForm.password" auto-complete="off" placeholder="密码"></el-input>
            </el-form-item>
            <el-form-item style="width: 100%">
                <el-button type="primary"
                    style="margin-left:40%;: 40%;background: #505458;border: none ;text-align: center"
                    v-on:click="login" :loading="loading">登录</el-button>


            </el-form-item>
        </el-form>
    </body>
</template>
<script>
export default {
    data() {
        return {
            rules: {
                username: [{ required: true, message: '用户名不能为空', trigger: 'blur' }],
                password: [{ required: true, message: '密码不能为空', trigger: 'blur' }]
            },
            checked: true,
            loginForm: {
                username: '',
                password: ''
            },
            loading: false
        }
    },
    methods: {
        login() {
            this.$router.push({path:"/"});
            // var _this = this
            // this.loading = true

            // _this.request.post('/Login/companyUserLogin', {
            //     userName: this.loginForm.username,
            //     password: this.loginForm.password
            // }).then(res => {
            //     this.loading = false
            //     if (res.code === 200) {
            //         localStorage.setItem("user", JSON.stringify(res.companyUser))
            //         console.log("success")
            //         this.$router.push("/")
            //     } else {
            //         _this.$alert(res.msg, '提示', {
            //             confirmButtonText: '确定'
            //         })
            //     }
            // }).catch(failResponse => { })


        }
    }
}
</script>
<style>
#paper {
    background: url("../assets/bg.jpg") no-repeat;
    background-position: center;
    height: 100%;
    width: 100%;
    background-size: cover;
    position: fixed;
}

body {
    margin: 0;
}

.header {
    margin-bottom: 10px;
}

.login-container {
    border-radius: 15px;
    background-clip: padding-box;
    margin: 60px auto;
    width: 350px;
    padding: 35px 35px 35px 35px;
    background: #fff;
    border: 1px solid #eaeaea;
    box-shadow: 0 0 25px #cac6c6;
}

.login_title {
    margin: 0px auto 40px auto;
    text-align: center;
    color: #505458;
}

.login_remember {
    margin: 0px 0px 35px 0px;
    text-align: left;
}
</style>
  