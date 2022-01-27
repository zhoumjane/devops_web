<template>
  <div class="user-form-container">
    <el-dialog title="添加用户" :visible.sync="addUserFormVisible">
      <el-form ref="addUserForm" :model="addUserForm">
        <el-form-item label="用户名" prop="username" :label-width="formLabelWidth">
          <el-input v-model="addUserForm.username" auto-complete="off" />
        </el-form-item>
        <el-form-item label="邮箱" prop="email" :label-width="formLabelWidth">
          <el-input v-model="addUserForm.email" auto-complete="off" />
        </el-form-item>
        <el-form-item label="密码" prop="password" :label-width="formLabelWidth">
          <el-input v-model="addUserForm.password" type="password" auto-complete="off" />
        </el-form-item>
        <el-form-item label="所属组" prop="groups" :label-width="formLabelWidth">
          <el-select v-model="addUserForm.groups" multiple filterable class="user-select" placeholder="请选择用户组" value="0">
            <el-option
              v-for="item in group_list"
              :key="item.index"
              :label="item.name"
              :value="item.id"
            />
          </el-select>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="handleAddUserCancel">取消</el-button>
        <el-button type="primary" @click="handleAddUserSubmit">确定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import { groupList } from '@/api/group'
export default {
  name: 'UserForm',
  props: {
    // eslint-disable-next-line vue/require-default-prop
    addUserFormVisible: {
      // eslint-disable-next-line no-undef
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      addUserForm: {
        username: '',
        password: '',
        email: '',
        groups: ''
      },
      group_list: [],
      state: 0,
      formLabelWidth: 'formLabelWidth'
    }
  },
  watch: {
    state() {
      groupList({ page_size: 100 }).then(res => {
        this.group_list = res.results
      })
    }
  },
  created() {
    this.state = 1
  },
  methods: {
    handleAddUserCancel() {
      this.$refs.addUserForm.resetFields()
      this.$emit('userFormCancelChild')
    },
    handleAddUserSubmit() {
      this.$emit('handleAddUserSubmitChild', this.addUserForm)
    }
  }
}
</script>

<style scoped>
  .user-form-container {
    position: relative;
  }
  .user-select{
    width: 100%;
  }
</style>
