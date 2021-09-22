<template>
  <div class="login-container">
    <div class="login-box">
      <!-- 网站图标 -->
      <div class="logo-box">
        <img class="logo" src="../assets/logo.png" alt="" />
      </div>
      <!-- 登录表单 -->
      <el-form
        class="form-box"
        ref="loginFormRef"
        :model="loginForm"
        :rules="rules"
      >
        <el-form-item prop="username">
          <el-input
            placeholder="请输入用户名"
            prefix-icon="iconfont icon-yonghu"
            v-model="loginForm.username"
          >
          </el-input>
        </el-form-item>
        <el-form-item prop="password">
          <el-input
            placeholder="请输入密码"
            prefix-icon="iconfont icon-mima"
            show-password
            v-model="loginForm.password"
          >
          </el-input>
        </el-form-item>
        <el-form-item class="btns">
          <el-button type="primary" @click="handleLogin">登录</el-button>
          <el-button type="info" @click="handleReset">重置</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      loginForm: {
        username: '',
        password: ''
      },
      rules: {
        username: [
          { required: true, message: '请输入用户名', trigger: 'blur' },
          { min: 3, max: 10, message: '长度在3到10个字符', trigger: 'blur' }
        ],
        password: [{ required: true, message: '请输入密码', trigger: 'blur' }]
      }
    }
  },
  mounted() {
    window.addEventListener('keydown', this.handleLogin, true)
  },
  methods: {
    // 重置
    handleReset() {
      this.$refs.loginFormRef.resetFields()
    },
    // 登录
    handleLogin() {
      this.$refs.loginFormRef.validate(async valid => {
        if (!valid) return
        const { data } = await this.$https.post('login', this.loginForm)
        if (data.meta.status !== 200) {
          this.$message.error(data.meta.msg)
          return
        }
        this.$message({
          message: '登录成功',
          type: 'success',
          duration: 600
        })
        window.sessionStorage.setItem('token', data.data.token)
        this.$router.push('home')
      })
    }
  }
}
</script>

<style lang="less" scoped>
.login-container {
  height: 100%;
  background-color: #c8ebdf;
  position: relative;
  .login-box {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 600px;
    height: 400px;
    background-color: #fff;
    border-radius: 20px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    .logo-box {
      position: absolute;
      left: 50%;
      top: 0;
      width: 100px;
      height: 100px;
      transform: translate(-50%, -50%);
      padding: 10px;
      border: 1px solid #eee;
      box-shadow: 0 0 10px #eee;
      border-radius: 50%;
      overflow: hidden;
      box-sizing: border-box;
      img {
        width: 100%;
        height: 100%;
        border: 1px solid #eee;
        border-radius: 50%;
        background-color: #e4f5ef;
        box-sizing: border-box;
      }
    }
  }
  .form-box {
    width: 80%;
    margin: 0 auto;
    margin-top: 10%;
    /deep/ input {
      display: block;
      width: 100%;
    }
    .btns {
      text-align: center;
    }
  }
}
</style>
