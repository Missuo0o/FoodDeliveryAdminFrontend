<template>
  <div class="addBrand-container">
    <HeadLable :goback="true"
               :title="title" />
    <div class="container">
      <el-form ref="ruleForm"
               :inline="false"
               :model="ruleForm"
               :rules="rules"
               class="demo-ruleForm"
               label-width="180px">
        <!--          <el-form-item label="员工职级" prop="region">-->
        <!--            <el-select v-model="ruleForm.region" placeholder="请选择品牌名称">-->
        <!--              <el-option label="区域一" value="shanghai"></el-option>-->
        <!--              <el-option label="区域二" value="beijing"></el-option>-->
        <!--            </el-select>-->
        <!--            <el-button @click="submitForm('ruleForm')" type="primary" class="continue" style="margin-left: 10px;" >+新增职级</el-button>-->
        <!--          </el-form-item>-->
        <el-form-item label="Username:"
                      prop="username">
          <el-input v-model="ruleForm.username"
                    maxlength="20"
                    placeholder="Please enter the username" />
        </el-form-item>
        <el-form-item label="Name:"
                      prop="name">
          <el-input v-model="ruleForm.name"
                    maxlength="12"
                    placeholder="Please enter the name" />
        </el-form-item>
        <!-- <el-form-item
          label="密码:"
          prop="password"
        >
          <el-input
            v-model="ruleForm.password"
            type="password"
            autocomplete="off"
            placeholder="请输入密码"
          />
        </el-form-item> -->
        <!-- <el-form-item
          label="确认密码:"
          prop="rePassword"
        >
          <el-input
            v-model="ruleForm.rePassword"
            type="password"
            autocomplete="off"
            placeholder="请输入确认密码"
          />
        </el-form-item> -->
        <el-form-item label="Phone:"
                      prop="phone">
          <el-input v-model="ruleForm.phone"
                    maxlength="10"
                    placeholder="Please enter the phone" />
        </el-form-item>
        <el-form-item label="Sex:"
                      prop="sex">
          <el-radio-group v-model="ruleForm.sex">
            <el-radio label="Male" />
            <el-radio label="Female" />
          </el-radio-group>
        </el-form-item>
        <el-form-item class="idNumber"
                      label="IdNumber:"
                      prop="idNumber">
          <el-input v-model="ruleForm.idNumber"
                    maxlength="9"
                    placeholder="Please enter the idNumber" />
        </el-form-item>
        <div class="subBox address">
          <!-- <el-form-item> -->
          <el-button @click="() => $router.push('/employee')">
            Cancel
          </el-button>
          <el-button :class="{ continue: actionType === 'add' }"
                     type="primary"
                     @click="submitForm('ruleForm', false)">
            Submit
          </el-button>
          <el-button v-if="actionType == 'add'"
                     type="primary"
                     @click="submitForm('ruleForm', true)">
            Submit and continue adding
          </el-button>
          <!-- </el-form-item> -->
        </div>
      </el-form>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import HeadLable from '@/components/HeadLable/index.vue'
import { addEmployee, editEmployee, queryEmployeeById } from '@/api/employee'

@Component({
  name: 'addShop',
  components: {
    HeadLable
  }
})
export default class extends Vue {
  private title = 'Add Employee'
  private actionType = ''
  private ruleForm = {
    name: '',
    phone: '',
    // 'password': '',
    // 'rePassword': '',
    sex: 'Male',
    idNumber: '',
    username: ''
  }

  // private validateRepassword (rule:any, value:any, callback:any) {
  //   if (value === '') {
  //     callback(new Error('请再次输入密码'))
  //   } else if (value !== this.ruleForm.password) {
  //     callback(new Error('两次输入密码不一致!'))
  //   } else {
  //     callback()
  //   }
  // }

  get rules() {
    return {
      name: [
        {
          required: true,
          // 'message': '请输入员工姓名',
          validator: (rule: any, value: string, callback: Function) => {
            if (!value) {
              callback(new Error('Please enter the name'))
            } else {
              const reg = /^[\u4e00-\u9fa5_a-zA-Z]{1,12}$/
              if (!reg.test(value)) {
                callback(new Error('The name input does not match, please enter 1-12 characters'))
              } else {
                callback()
              }
              callback()
            }
          },
          trigger: 'blur'
        }
      ],
      username: [
        {
          required: true,
          // message: '请输入账号',
          validator: (rule: any, value: string, callback: Function) => {
            if (!value) {
              callback(new Error('Please enter the username'))
            } else {
              const reg = /^([a-z]|[0-9]){3,20}$/
              if (!reg.test(value)) {
                callback(new Error('Account number input does not match, please enter 3-20 characters'))
              } else {
                callback()
              }
            }
          },
          trigger: 'blur'
        }
      ],
      phone: [{ required: true, validator: this.checkphone, trigger: 'blur' }],
      idNumber: [{ required: true, validator: this.validID, trigger: 'blur' }]
    }
  }

  created() {
    this.actionType = this.$route.query.id ? 'edit' : 'add'
    if (this.$route.query.id) {
      this.title = 'Update Employee'
      this.init()
    }
  }

  private isCellPhone(val: any) {
    // 更新正则表达式以匹配9位数字的美国手机号格式
    if (!/^\d{10}$/.test(val)) {
      return false;
    } else {
      return true;
    }
  }
  private isIdnumber(val: any) {
    if (!/^\d{9}$/.test(val)) {
      return false;
    } else {
      return true;
    }
  }

  private checkphone(rule: any, value: any, callback: any) {
    // let phoneReg = /(^1[3|4|5|6|7|8|9]\d{9}$)|(^09\d{8}$)/;
    if (value == '') {
      callback(new Error('Please enter the phone number'))
    } else if (!this.isCellPhone(value)) {
      //引入methods中封装的检查手机格式的方法
      callback(new Error('Please enter the correct number!'))
    } else {
      callback()
    }
  }

  private validID(rule: any, value: any, callback: any) {
    // 身份证号码为15位或者18位，15位时全为数字，18位前17位为数字，最后一位是校验位，可能为数字或字符X
    // let reg = /(^\d{15}$)|(^\d{18}$)|(^\d{17}(\d|X|x)$)/
    if (value == '') {
      callback(new Error('Please enter the idNumber'))
    }
    else if (!this.isIdnumber(value)){
      callback(new Error('Please enter the correct idNumber!'))
    } else {
      callback()
    }
  }

  private async init() {
    const id = this.$route.query.id
    queryEmployeeById(id).then((res: any) => {
      // String(res.status) === '200'
      if (res.data.code === 1) {
        this.ruleForm = res.data.data
        this.ruleForm.sex = res.data.data.sex === '0' ? 'Female' : 'Male'
        // this.ruleForm.password = ''
      } else {
        this.$message.error(res.data.msg)
      }
      // if (res.data.code == 200) {
      //   const { data } = res.data
      //   this.ruleForm = data
      //   this.ruleForm.password = ''
      //   // this.ruleForm.rePassword = '' //JSON.parse(JSON.stringify(data.password));
      // } else {
      //   this.$message.error(res.data.desc)
      // }
    })
  }

  private submitForm(formName: any, st: any) {
    ;(this.$refs[formName] as any).validate((valid: any) => {
      if (valid) {
        if (this.actionType === 'add') {
          const params = {
            ...this.ruleForm,
            sex: this.ruleForm.sex === 'Female' ? 0 : 1
          }
          addEmployee(params)
            .then((res: any) => {
              if (res.data.code === 1) {
                this.$message.success('Success ！')
                if (!st) {
                  this.$router.push({ path: '/employee' })
                } else {
                  this.ruleForm = {
                    username: '',
                    name: '',
                    phone: '',
                    // 'password': '',
                    // npm'rePassword': '',/
                    sex: 'Male',
                    idNumber: ''
                  }
                }
              } else {
                this.$message.error(res.data.msg)
              }
            })
            .catch(() => {
              // this.$message.error(res.data.msg)
            })
        } else {
          const params = {
            ...this.ruleForm,
            sex: this.ruleForm.sex === 'Female' ? 0 : 1
          }
          editEmployee(params)
            .then((res: any) => {
              if (res.data.code === 1) {
                this.$message.success('Success ！')
                this.$router.push({ path: '/employee' })
              } else {
                this.$message.error(res.data.msg)
              }
            })
            .catch(() => {
              // this.$message.error('请求出错了：' + err.message)
            })
        }
      } else {
        return false
      }
    })
  }

}
</script>

<style lang="scss" scoped>
.addBrand {
  &-container {
    margin: 30px;
    margin-top: 0px;

    .HeadLable {
      background-color: transparent;
      margin-bottom: 0px;
      padding-left: 0px;
    }

    .container {
      position: relative;
      z-index: 1;
      background: #fff;
      padding: 30px;
      border-radius: 4px;
      // min-height: 500px;
      .subBox {
        padding-top: 30px;
        text-align: center;
        border-top: solid 1px $gray-5;
      }
    }

    .idNumber {
      margin-bottom: 39px;
    }

    .el-form-item {
      margin-bottom: 29px;
    }

    .el-input {
      width: 293px;
    }
  }
}
</style>
