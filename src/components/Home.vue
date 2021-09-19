<template>
  <el-container class="home-container">
    <el-header>
      <router-link class="header-left" to="/home">
        <img class="logo" src="../assets/logo.png" alt="" />
        <span v-text="title"></span>
      </router-link>
      <el-button @click="handleLogout">退出</el-button>
    </el-header>
    <el-container>
      <el-aside width="200px">
        <el-menu
          class="menu-item-1"
          unique-opened
          @open="handleOpen"
          @close="handleClose"
        >
          <!-- 一级菜单 -->
          <el-submenu
            v-for="menu in menuList"
            :key="menu.id"
            :index="menu.id + ''"
          >
            <template slot="title">
              <i class="el-icon-location"></i>
              <span>{{ menu.authName }}</span>
            </template>
            <!-- 二级菜单 -->
            <el-menu-item
              v-for="submenu in menu.children"
              :key="submenu.id"
              :index="submenu.id + ''"
            >
              <i class="el-icon-menu"></i>
              <span slot="title">{{ submenu.authName }}</span>
            </el-menu-item>
          </el-submenu>
        </el-menu>
      </el-aside>
      <el-main>Main</el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {
  data() {
    return {
      title: 'XX电商管理系统',
      menuList: [],
      iconList: []
    }
  },
  created() {
    this.getMenu()
  },
  methods: {
    handleLogout() {
      window.sessionStorage.removeItem('token')
      this.$router.push('login')
    },
    // 获取菜单项
    async getMenu() {
      const { data } = await this.$https.get('menus')
      if (data.meta.status !== 200) {
        this.$message.error(data.meta.msg)
        return
      }
      console.log(data.data)
      this.menuList = data.data
    },
    handleOpen(key, keyPath) {
      console.log(key, keyPath)
    },
    handleClose(key, keyPath) {
      console.log(key, keyPath)
    }
  }
}
</script>

<style lang="less" scoped>
.home-container {
  height: 100%;
  background-color: #e4f5ef;
  .el-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    background-color: #8ea59d;
    .header-left {
      display: flex;
      align-items: center;
      text-decoration: none;
      .logo {
        width: 40px;
        height: 40px;
        margin-right: 10px;
        border: 1px solid #eee;
        border-radius: 50%;
        background-color: #e4f5ef;
      }
      span {
        font-size: 20px;
        color: #000;
      }
    }
  }
  .el-aside {
    background-color: #c8ebdf;
  }
}
</style>
