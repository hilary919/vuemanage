<template>
  <div class="users-container">
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>活动管理</el-breadcrumb-item>
      <el-breadcrumb-item>活动列表</el-breadcrumb-item>
    </el-breadcrumb>
    <el-card class="users-card">
      <div class="">
        <el-row :gutter="20">
          <el-col :span="10">
            <el-input
              placeholder="请输入内容"
              v-model="searchStr"
              class="input-with-select"
              clearable
              @clear="handleSearch"
            >
              <el-button
                slot="append"
                icon="el-icon-search"
                @click="handleSearch"
              ></el-button>
            </el-input>
          </el-col>
          <el-col :span="6">
            <el-button type="primary" @click="handleAdd">添加用户</el-button>
          </el-col>
        </el-row>
      </div>
      <div>
        <el-table :data="tableData" style="width: 100%" border stripe>
          <el-table-column type="index" label="#"> </el-table-column>
          <el-table-column prop="username" label="姓名"> </el-table-column>
          <el-table-column prop="email" label="邮箱"> </el-table-column>
          <el-table-column prop="mobile" label="电话"> </el-table-column>
          <el-table-column prop="role_name" label="角色"> </el-table-column>
          <el-table-column label="状态">
            <template slot-scope="scope">
              <el-switch
                v-model="scope.row.mg_state"
                @change="handleChangeStatus(scope.row)"
              >
              </el-switch>
            </template>
          </el-table-column>
          <el-table-column label="操作" width="180px">
            <template slot-scope="scope">
              <el-tooltip content="编辑" placement="top" :enterable="false">
                <el-button
                  icon="el-icon-edit"
                  type="primary"
                  size="mini"
                  @click="handleEdit(scope.$index, scope.row)"
                ></el-button>
              </el-tooltip>
              <el-tooltip content="删除" placement="top" :enterable="false">
                <el-button
                  icon="el-icon-delete"
                  type="danger"
                  size="mini"
                  @click="handleDelete(scope.$index, scope.row)"
                ></el-button>
              </el-tooltip>
              <el-tooltip content="分配权限" placement="top" :enterable="false">
                <el-button
                  icon="el-icon-setting"
                  type="warning"
                  size="mini"
                  @click="handleDelete(scope.$index, scope.row)"
                ></el-button>
              </el-tooltip>
            </template>
          </el-table-column>
        </el-table>
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="pageData.pagenum"
          :page-sizes="pageSizeArr"
          :page-size="pageData.pagesize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="totalNum"
        >
        </el-pagination>
      </div>
    </el-card>
    <!-- 添加用户对话框 -->
    <el-dialog
      title="添加用户"
      :visible.sync="addDialogVisible"
      width="50%"
      @close="handleCloseAddForm"
    >
      <el-form :model="addForm" :rules="addRules" ref="addFormRef">
        <el-form-item
          label="用户名"
          prop="username"
          :label-width="formLabelWidth"
        >
          <el-input v-model="addForm.username"></el-input>
        </el-form-item>
        <el-form-item
          label="密码"
          prop="password"
          :label-width="formLabelWidth"
        >
          <el-input v-model="addForm.password" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="邮箱" prop="email" :label-width="formLabelWidth">
          <el-input v-model="addForm.email"></el-input>
        </el-form-item>
        <el-form-item
          label="手机号"
          prop="mobile"
          :label-width="formLabelWidth"
        >
          <el-input v-model="addForm.mobile"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="addDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="handleAddConfirm">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>
<script>
export default {
  data() {
    var checkMobile = (rule, value, callback) => {
      if (!value) {
        return callback(new Error('请输入手机号码'))
      }
      setTimeout(() => {
        const regMobile = /^1[3,4,5,6,7,8,9][0-9]{9}$/
        if (!regMobile.test(value)) {
          return callback(new Error('请输入正确的手机号'))
        }
        callback()
      }, 1000)
    }
    return {
      tableData: [],
      searchStr: '',
      pageData: {
        query: '', // 查询参数
        pagenum: 1, // 当前页
        pagesize: 2 // 每页显示条数
      },
      pageSizeArr: [2, 5, 10],
      totalNum: 0, // 查询结果总数
      formLabelWidth: '70px',
      addDialogVisible: false, // 添加用户对话框显示与否
      addForm: {
        username: '',
        password: '',
        email: '',
        mobile: ''
      },
      addRules: {
        username: [
          { required: true, message: '请输入用户名', trigger: 'blur' }
        ],
        password: [{ required: true, message: '请输入密码', trigger: 'blur' }],
        email: [
          { required: true, message: '请输入邮箱', trigger: 'blur' },
          {
            type: 'email',
            message: '请输入正确的邮箱地址',
            trigger: ['blur', 'change']
          }
        ],
        mobile: [{ validator: checkMobile, trigger: 'blur' }]
      }
    }
  },
  created() {
    this.getData()
  },
  methods: {
    // 获取用户列表数据
    async getData() {
      const { data } = await this.$https.get('users', { params: this.pageData })
      if (data.meta.status !== 200) {
        this.$message.error(data.meta.msg)
        return
      }
      this.totalNum = data.data.total
      this.tableData = data.data.users
    },
    // 查询
    handleSearch() {
      if (this.pageData.query === this.searchStr) return
      this.pageData.query = this.searchStr
      this.getData()
    },
    // 切换状态
    async handleChangeStatus(row) {
      const { data } = await this.$https.put(
        `users/${row.id}/state/${!row.mg_state}`
      )
      if (data.meta.status !== 200) {
        this.$message.error(data.meta.msg)
        return
      }
      this.$message({
        message: '状态切换成功',
        type: 'success'
      })
    },
    // 添加
    handleAdd() {
      this.addDialogVisible = true
    },
    async handleAddConfirm() {
      this.$refs.addFormRef.validate(async valid => {
        if (!valid) return
        const { data } = await this.$https.post('users', this.addForm)
        console.log(data)
        if (data.meta.status !== 201) {
          this.$message.error(data.meta.msg)
          return
        }
        this.$message({
          message: '添加成功',
          type: 'success'
        })
        this.addDialogVisible = false
      })
    },
    // 关闭对话框清空表单数据
    handleCloseAddForm() {
      this.$refs.addFormRef.resetFields()
    },
    // 编辑
    handleEdit(index, row) {
      console.log(index, row)
    },
    // 删除
    handleDelete(index, row) {
      console.log(index, row)
    },
    // 每页数量改变
    handleSizeChange(val) {
      this.pageData.pagesize = val
      this.getData()
    },
    // 分页点击
    handleCurrentChange(val) {
      this.pageData.pagenum = val
      this.getData()
    }
  }
}
</script>
