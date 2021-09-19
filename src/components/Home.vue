<template>
  <div class="login-container">
    <div class="login-box">
      <el-button @click="handleExit">退出</el-button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {}
  },
  methods: {
    handleExit() {
      window.sessionStorage.removeItem('token')
      this.$router.push('login')
    }
  }
}
</script>

<style lang="less" scoped>
</style>
