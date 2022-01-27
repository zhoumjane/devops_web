<template>
  <div class="user-list-container">
    <div class="header">
      <el-row>
        <el-col :span="8">
          <el-input v-model="params.search" placeholder="请输入内容" @keyup.enter.native="searchUserName">
            <el-button slot="append" type="primary" icon="el-icon-search" @click="searchUserName">搜索</el-button>
          </el-input>
        </el-col>
        <el-col ref="addUserFormParent">
          <user-form :add-user-form-visible="UserFormVisible" @userFormCancelChild="userFormCancelParent" @handleAddUserSubmitChild="handleAddUserSubmitParent" />
          <user-modify :modify-user-form-visible="modifyUserFormVisible" :modify-user-form="modifyUserForm" @userFormModifyCancelChild="userFormModifyCancelParent" @handleModifyUserSubmitChild="handleModifyUserSubmitParent" />
        </el-col>
      </el-row>
    </div>
    <el-table :data="userData" :show-header="true" :header-cell-style="{background: '#fafafa', color: 'rgba(0,0,0,.85)'}" border class="table">
      <el-table-column prop="username" label="用户名" />
      <el-table-column prop="email" label="email地址" />
      <el-table-column prop="groups" label="所属组" type="scope">
        <template slot-scope="scope">
          <div v-for="(item, index) in scope.row.groups" :key="index" :label="item.name" :value="item.id">
            <div class="group-name">{{ item.name }}</div>
          </div>
        </template>
      </el-table-column>
      <el-table-column prop="user_permissions" label="权限" type="scope">
        <template slot-scope="scope">
          <div v-for="(item, index) in scope.row.user_permissions" :key="index" :label="item.name" :value="item.id">
            <div class="permission-name">{{ item.name }}</div>
          </div>
        </template>
      </el-table-column>
      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-button size="mini" type="primary" class="el-icon-edit" @click="handleUserEdit(scope.row)">编辑</el-button>
        </template>
      </el-table-column>
    </el-table>
    <div v-show="totalNum > 10" class="pagination">
      <el-pagination :page-size="10" :pager-count="11" layout="total, prev, pager, next, jumper" :total="totalNum" @current-change="handleUserCurrent" />
    </div>
  </div>
</template>

<script>
// eslint-disable-next-line no-unused-vars
import { userList, userCreate, userUpdate } from '@/api/user'
// eslint-disable-next-line no-unused-vars
import userForm from './userformadd'
import userModify from './userformmodify'
export default {
  name: 'Index',
  components: {
    userForm,
    userModify
  },
  data() {
    return {
      userData: [],
      params: {
        page: 1
      },
      UserFormVisible: false,
      modifyUserFormVisible: false,
      totalNum: 0,
      modifyUserForm: {
        username: '',
        password: '',
        email: '',
        groups: [],
        user_permissions: []
      }
    }
  },
  created() {
    this.fetchData()
  },
  methods: {
    fetchData() {
      userList(this.params).then(res => {
        this.totalNum = res.count
        this.userData = res.results
      })
    },
    handleAddUser() {
      this.UserFormVisible = true
    },
    searchUserName() {
      this.params.page = 1
      this.fetchData()
    },
    handleUserEdit(obj) {
      this.modifyUserForm = obj
      this.modifyUserForm['groups'] = this.modifyUserForm['groups'].map(it => it.id)
      this.modifyUserForm['user_permissions'] = this.modifyUserForm['user_permissions'].map(it => it.id)
      this.modifyUserFormVisible = true
    },
    // handleUserDelete(id) {
    //   this.$confirm('是否确认删除用户?', '提示', {
    //     confirmButtonText: '确定',
    //     cancelButtonText: '取消',
    //     type: 'warning'
    //   }).then(() => {
    //     userDel(id).then(() => {
    //       this.$message({
    //         message: '删除成功',
    //         type: 'success'
    //       })
    //       this.fetchData()
    //     })
    //   }).catch(() => {
    //     this.$message({
    //       type: 'info',
    //       message: '已取消删除'
    //     })
    //   })
    // },
    handleUserCurrent(val) {
      this.params.page = val
      this.fetchData()
    },
    userFormCancelParent() {
      this.UserFormVisible = false
    },
    userFormModifyCancelParent() {
      this.modifyUserFormVisible = false
      this.fetchData()
    },
    handleAddUserSubmitParent(data) {
      userCreate(data).then(res => {
        this.$message({
          message: '创建用户成功',
          type: 'success'
        })
        this.UserFormVisible = false
        // this.$refs.GroupForm.$refs.addGroupForm.resetFields()
        this.fetchData()
      })
    },
    handleModifyUserSubmitParent(data) {
      userUpdate(data.id, data).then(res => {
        this.$message({
          message: '修改成功',
          type: 'success'
        })
        this.modifyUserFormVisible = false
        this.fetchData()
      })
    }
  }
}
</script>

<style scoped>
  .user-list-container {
    position: relative;
  }
  .header{
    margin-top: 15px;
    margin-bottom: 20px;
  }
  .table{
    width: 100%;
  }
  .group-name{
    cursor:pointer;
  }
  .permission-name{
    cursor:pointer;
    font-size: xx-small;
    color: green;
  }
  .pagination{
    text-align: right;
    margin-top: 20px;
  }
</style>
