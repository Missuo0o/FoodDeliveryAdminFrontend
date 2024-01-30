<template>
  <el-dialog
    :visible.sync="dialogFormVisible"
    class="pwdCon"
    title="Update Password"
    width="568px"
    @close="handlePwdClose()"
  >
    <el-form ref="form" :model="form" :rules="rules" label-width="85px">
      <el-form-item label="Original:" prop="oldPassword">
        <el-input
          v-model="form.oldPassword"
          placeholder="Please enter"
          type="password"
        ></el-input>
      </el-form-item>
      <el-form-item label="New:" prop="newPassword">
        <el-input
          v-model="form.newPassword"
          placeholder="6 - 20 digit password, numeric or alphabetic, case sensitive"
          type="password"
        ></el-input>
      </el-form-item>
      <el-form-item label="Confirm:" prop="affirmPassword">
        <el-input
          v-model="form.affirmPassword"
          placeholder="Please enter"
          type="password"
        ></el-input>
      </el-form-item>
    </el-form>
    <div slot="footer" class="dialog-footer">
      <el-button @click="handlePwdClose()">Cancel</el-button>
      <el-button type="primary" @click="handleSave()">Submit</el-button>
    </div>
  </el-dialog>
</template>
<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator'
import { Form as ElForm } from 'element-ui'
// 接口
import { editPassword } from '@/api/users'

@Component({
  name: 'Password'
})
export default class extends Vue {
  @Prop() private dialogFormVisible!: any
  private form = {} as any
  private affirmPassword = ''

  handleSave() {
    ;(this.$refs.form as ElForm).validate(async (valid: boolean) => {
      if (valid) {
        const parnt = {
          oldPassword: this.form.oldPassword,
          newPassword: this.form.newPassword
        }
        await editPassword(parnt)
        this.$emit('handleclose')
        ;(this.$refs.form as ElForm).resetFields()
      } else {
        return false
      }
    })
  }

  handlePwdClose() {
    ;(this.$refs.form as ElForm).resetFields()
    this.$emit('handleclose')
  }

  private validatePwd = (rule: any, value: any, callback: Function) => {
    const reg = /^[0-9A-Za-z]{6,20}$/
    if (!value) {
      callback(new Error('Please enter'))
    } else if (!reg.test(value)) {
      callback(new Error('6 - 20 digit password, numeric or alphabetic, case sensitive'))
    } else {
      callback()
    }
  }

  private validatePass2 = (rule, value, callback) => {
    if (!value) {
      callback(new Error('Please enter your password again'))
    } else if (value !== this.form.newPassword) {
      callback(new Error('Password is not the same, please re-enter the password'))
    } else {
      callback()
    }
  }

  rules = {
    oldPassword: [{ validator: this.validatePwd, trigger: 'blur' }],
    newPassword: [{ validator: this.validatePwd, trigger: 'blur' }],
    affirmPassword: [{ validator: this.validatePass2, trigger: 'blur' }]
  }
}
</script>
<style lang="scss">
.navbar {
  .pwdCon {
    .el-dialog__body {
      padding-top: 60px;
      padding: 60px 100px 0;
    }

    .el-input__inner {
      padding: 0 12px;
    }

    .el-form-item {
      margin-bottom: 26px;
    }

    .el-form-item__label {
      text-align: left;
    }

    .el-dialog__footer {
      padding-top: 14px;
    }
  }
}
</style>
