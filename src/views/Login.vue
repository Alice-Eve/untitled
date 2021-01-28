<template>
<div id="wrapper">
  <div id="head">
    <div class="mod-header">
      <a href=""></a>
    </div>
  </div>

  <div id="content">
    <div class="login-container">
      <div class="login-form">
        <el-form
            :rules="rules"
            ref="loginForm"
            :model="loginForm"
            class="pass-form"
            @keydown.enter.native="submitLogin">
          <p class="pass-form-logo">人事系统登录</p>
          <el-form-item prop="username">
            <el-input size="normal" type="text" v-model="loginForm.username" auto-complete="off"
                      placeholder="请输入账号"></el-input>
          </el-form-item>
          <el-form-item prop="password">
            <el-input size="normal" type="password" v-model="loginForm.password" auto-complete="off"
                      placeholder="请输入密码"></el-input>
          </el-form-item>
          <el-checkbox size="normal" class="loginRemember" v-model="checked">记住账号</el-checkbox>
          <el-button size="normal" type="primary" style="width: 100%;" @click="submitLogin">登录</el-button>
        </el-form>
      </div>
    </div>
  </div>
</div>
</template>

<script>

export default {
  name: "Login",
  data() {
    return {
      loginForm: {
        username: 'admin',
        password: '123',
      },
      checked: true,
      rules: {
        username: [{required: true, message: '请输入用户名', trigger: 'blur'}],
        password: [{required: true, message: '请输入密码', trigger: 'blur'}],
      }
    }
  },
  methods: {
    submitLogin() {
      this.$refs.loginForm.validate((valid) => {
        if (valid) {
          this.postKeyValueRequest('/login',this.loginForm).then(resp=>{
            if (resp) {
              this.$store.commit('INIT_CURRENTHR', resp.obj);
              window.sessionStorage.setItem("user",JSON.stringify(resp.obj));
              let path = this.$route.query.redirect;
              this.$router.replace((path == '/' || path == undefined) ? '/home' : path);
            }
          })
        } else {
          return false;
        }
      });
    }
  }
}
</script>

<style>
#head {
  padding-top: 60px;
}
#head, #content {
  width: 980px;
  margin-left: auto;
  margin-right: auto;
}
#head {
  height: 75px;
  z-index: 100;
}
#wrapper {
  background: #fff;
  height: auto;
  position: relative;
  min-height: 100%;
  _height: 100%;
  width: 920px;
  margin: 0 auto;
}
#content {
  margin-top: 16%;
  width: 920px;
  background: url(https://passport.baidu.com/static/passpc-account/img/login/login-bg.png) no-repeat 0 center;
  padding-bottom: 40px;
  display: block;
}
.mod-header {
  display: inline;
  font-size: 66px;
}
.mod-header a {
  display: block;
  width: 186px;
  height: 75px;
  background-repeat: no-repeat;
  background-position: 0 0;
}
.login-container{
  _height: 331px;
  min-height: 331px;
}
.pass-form{
  padding: 0 28px;
}
p.pass-form-logo {
  background: url(https://passport.baidu.com/passApi/img/loginlogo.png) no-repeat left;
  font-size: 16px;
  height: 32px;
  line-height: 46px;
  padding-left: 106px;
  color: #000;
  margin-bottom: 15px;
}
.login-form {
  float: right;
  width: 354px;
  font-family: "Microsoft Yahei","微软雅黑",Arial,STHeiti;
  border: 1px solid #efefef;
  box-shadow: 1px 1px 1px #efefef;
  padding: 26px 0 40px;
  border-radius: 15px;
}
.loginRemember {
  text-align: left;
  margin: 0px 0px 15px 0px;
}
</style>