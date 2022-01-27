<template>
  <div class="user-modify-container">
    <el-dialog title="编辑" :visible.sync="modifyUserFormVisible" :before-close="handleModifyUserCancel">
      <el-form ref="modifyUserForm" :model="modifyUserForm">
        <el-form-item label="所属组" prop="groups" :label-width="formLabelWidth">
          <el-select v-model="modifyUserForm.groups" multiple filterable class="user-select" placeholder="请选择用户组" value="0">
            <el-option
              v-for="item in group_list"
              :key="item.index"
              :label="item.name"
              :value="item.id"
            />
          </el-select>
        </el-form-item>
        <el-form-item label="权限" prop="user_permissions" :label-width="formLabelWidth">
          <el-select v-model="modifyUserForm.user_permissions" multiple filterable class="user-select" placeholder="请选择权限" value="0">
            <el-option
              v-for="item in permissions_list"
              :key="item.index"
              :label="item.name"
              :value="item.id"
            />
          </el-select>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="handleModifyUserCancel">取消</el-button>
        <el-button type="primary" @click="handleModifyUserSubmit">确定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import { groupList, permissionsList } from '@/api/group'
export default {
  name: 'UserModify',
  props: {
    // eslint-disable-next-line vue/require-default-prop
    modifyUserFormVisible: {
      // eslint-disable-next-line no-undef
      type: Boolean,
      default: false
    },
    modifyUserForm: {
      type: Object,
      // eslint-disable-next-line vue/require-valid-default-prop
      default: function() {
        return {
          username: '',
          password: '',
          email: '',
          groups: [],
          user_permissions: []
        }
      }
    }
  },
  data() {
    return {
      group_list: [],
      permissions_list: [],
      state: 0,
      formLabelWidth: 'formLabelWidth'
    }
  },
  watch: {
    state() {
      groupList({ page_size: 100 }).then(res => {
        this.group_list = res.results
      })
      permissionsList({ page_size: 100 }).then(res => {
        this.permissions_list = res.results
      })
    }
  },
  created() {
    this.state = 1
  },
  methods: {
    handleModifyUserCancel() {
      this.$refs.modifyUserForm.resetFields()
      this.$emit('userFormModifyCancelChild')
    },
    handleModifyUserSubmit() {
      this.$emit('handleModifyUserSubmitChild', this.modifyUserForm)
    }
  }
}
</script>

<style scoped>
  .user-modify-container {
    position: relative;
  }
  .user-select{
    width: 100%;
  }
</style>
