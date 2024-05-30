<template>
  <div class="container">
    <div style="height: 60px; position: fixed; top: 0; display: flex;">
      <img src="../assets/imgs/logo.png" alt="" style="width: 50px">
      <span style="color: #409EFF; font-size: 30px; font-weight: bold; margin-top: 10px; margin-left: 15px">2 Cloud</span>
    </div>
    <div style="display: flex; justify-content: center; width: 100%">

      <div style="width: 350px; padding: 50px 30px; box-shadow: 0 0 10px rgba(0,0,0,.2); background-color: rgba(181,156,231,0.56); border-radius: 5px;">
      <div style="text-align: center; font-size: 24px; font-weight: bold; margin-bottom: 30px; color: #333">欢 迎 注 册</div>
      <el-form :model="form" :rules="rules" ref="formRef">
        <el-form-item prop="username">
          <el-input size="medium" prefix-icon="el-icon-user" placeholder="请输入账号" v-model="form.username"></el-input>
        </el-form-item>
        <el-form-item prop="password">
          <el-input size="medium" prefix-icon="el-icon-lock" placeholder="请输入密码" show-password  v-model="form.password"></el-input>
        </el-form-item>
        <el-form-item prop="confirmPass">
          <el-input size="medium" prefix-icon="el-icon-lock" placeholder="请确认密码" show-password  v-model="form.confirmPass"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button style="width: 100%; background-color: #2a60c9; border-color: #2a60c9; color: white" @click="register">注 册</el-button>
        </el-form-item>
        <div style="display: flex; align-items: center">
          <div style="flex: 1"></div>
          <div style="flex: 1; text-align: right">
            已有账号？请 <a href="/login">登录</a>
          </div>
        </div>
      </el-form>
    </div>
    </div>
  </div>

</template>

<script>
export default {
  name: "Register",
  data() {
    // 验证码校验
    const validatePassword = (rule, confirmPass, callback) => {
      if (confirmPass === '') {
        callback(new Error('请确认密码'))
      } else if (confirmPass !== this.form.password) {
        callback(new Error('两次输入的密码不一致'))
      } else {
        callback()
      }
    }
    return {
      form: {role: 'USER'},
      rules: {
        username: [
          { required: true, message: '请输入账号', trigger: 'blur' },
        ],
        password: [
          { required: true, message: '请输入密码', trigger: 'blur' },
        ],
        confirmPass: [
          { validator: validatePassword, trigger: 'blur' }
        ]
      }
    }
  },
  created() {

  },
  methods: {
    register() {
      this.$refs['formRef'].validate((valid) => {
        if (valid) {
          // 验证通过
          this.$request.post('/register', this.form).then(res => {
            if (res.code === '200') {
              this.$router.push('/')  // 跳转登录页面
              this.$message.success('注册成功')
            } else {
              this.$message.error(res.msg)
            }
          })
        }
      })
    }
  }
}
</script>

<style scoped>
.container {
  height: 100vh;
  overflow: hidden;
  //background-image: url("@/assets/imgs/bg1.jpg");
  background-size: 100%;
  display: flex;
  align-items: center;
  //justify-content: center;
  //color: #666;
}
a {
  color: #2a60c9;
}
</style>