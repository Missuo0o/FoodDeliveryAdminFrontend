<template>
  <div class="addBrand-container">
    <div class="container">
      <el-form ref="ruleForm"
               :inline="true"
               :model="ruleForm"
               :rules="rules"
               class="demo-ruleForm"
               label-width="180px">
        <div>
          <el-form-item label="Name:"
                        prop="name">
            <el-input v-model="ruleForm.name"
                      maxlength="20"
                      placeholder="Please enter the name" />
          </el-form-item>
          <el-form-item label="Category:"
                        prop="idType">
            <el-select v-model="ruleForm.idType"
                       placeholder="Please select the category"
                       @change="$forceUpdate()">
              <el-option v-for="(item, index) in setMealList"
                         :key="index"
                         :label="item.name"
                         :value="item.id" />
            </el-select>
          </el-form-item>
        </div>
        <div>
          <el-form-item label="Price:"
                        prop="price">
            <el-input v-model="ruleForm.price"
                      maxlength="5"
                      placeholder="Please set the price" />
          </el-form-item>
        </div>
        <div>
          <el-form-item label="Dish:"
                        required>
            <el-form-item>
              <div class="addDish">
                <span v-if="dishTable.length == 0"
                      class="addBut"
                      @click="openAddDish('new')">
                  + Add Dish</span>
                <div v-if="dishTable.length != 0"
                     class="content">
                  <div class="addBut"
                       style="margin-bottom: 20px"
                       @click="openAddDish('change')">
                    + Add Dish
                  </div>
                  <div class="table">
                    <el-table :data="dishTable"
                              style="width: 100%">
                      <el-table-column align="center"
                                       label="Name"
                                       prop="name"
                                       width="180" />
                      <el-table-column align="center"
                                       label="Price"
                                       prop="price"
                                       width="180">
                        <template slot-scope="scope">
                          {{ (Number(scope.row.price).toFixed(2) * 100) / 100 }}
                        </template>
                      </el-table-column>
                      <el-table-column align="center"
                                       label="Number"
                                       prop="address">
                        <template slot-scope="scope">
                          <el-input-number v-model="scope.row.copies"
                                           :max="99"
                                           :min="1"
                                           label="Description"
                                           size="small" />
                        </template>
                      </el-table-column>
                      <el-table-column align="center"
                                       label="Operate"
                                       prop="address"
                                       width="180px;">
                        <template slot-scope="scope">
                          <el-button class="delBut non"
                                     size="small"
                                     type="text"
                                     @click="delDishHandle(scope.$index)">
                            Delete
                          </el-button>
                        </template>
                      </el-table-column>
                    </el-table>
                  </div>
                </div>
              </div>
            </el-form-item>
          </el-form-item>
        </div>
        <div>
          <el-form-item label="Image:"
                        prop="image"
                        required>
            <image-upload :prop-image-url="imageUrl"
                          @imageChange="imageChange">
              Image size not more than 2M<br>Only PNG JPEG JPG type images can be uploaded<br>It is recommended to
              upload a 200*200 or 300*300 size image
            </image-upload>
          </el-form-item>
        </div>
        <!-- <el-form-item
          label="商品码:"
          prop="code"
        >
          <el-input
            v-model="ruleForm.code"
            placeholder="请输入商品码"
          />
        </el-form-item> -->
        <div class="address">
          <el-form-item label="Description:">
            <el-input v-model="ruleForm.description"
                      :rows="3"
                      maxlength="200"
                      placeholder="Package description, maximum 200 words"
                      type="textarea" />
          </el-form-item>
        </div>
        <div class="subBox address">
          <el-form-item>
            <el-button @click="() => $router.back()">
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
          </el-form-item>
        </div>
      </el-form>
    </div>
    <el-dialog v-if="dialogVisible"
               :before-close="handleClose"
               :visible.sync="dialogVisible"
               class="addDishList"
               title="Add Dish"
               width="60%">
      <el-input v-model="value"
                class="seachDish"
                clearable
                placeholder="Please enter the name of the dish"
                size="small"
                style="width: 293px; height: 40px">
        <i slot="prefix"
           class="el-input__icon el-icon-search"
           style="cursor: pointer"
           @click="seachHandle" />
      </el-input>
      <AddDish v-if="dialogVisible"
               ref="adddish"
               :check-list="checkList"
               :dish-list="dishList"
               :seach-key="seachKey"
               @checkList="getCheckList" />
      <span slot="footer"
            class="dialog-footer">
        <el-button @click="handleClose">Cancel</el-button>
        <el-button type="primary"
                   @click="addTableList">Submit</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import HeadLable from '@/components/HeadLable/index.vue'
import ImageUpload from '@/components/ImgUpload/index.vue'
import AddDish from './components/AddDish.vue'
import { addSetmeal, editSetmeal, querySetmealById } from '@/api/setMeal'
import { getCategoryList } from '@/api/dish'

@Component({
  name: 'addShop',
  components: {
    HeadLable,
    AddDish,
    ImageUpload
  }
})
export default class extends Vue {
  private value: string = ''
  private setMealList: [] = []
  private seachKey: string = ''
  private dishList: [] = []
  private imageUrl: string = ''
  private actionType: string = ''
  private dishTable: [] = []
  private dialogVisible: boolean = false
  private checkList: any[] = []
  private ruleForm = {
    name: '',
    categoryId: '',
    price: '',
    code: '',
    image: '',
    description: '',
    dishList: [],
    status: true,
    idType: ''
  }

  get rules() {
    return {
      name: {
        required: true,
        validator: (rule: any, value: string, callback: Function) => {
          if (!value) {
            callback(new Error('Please enter the name'))
          } else {
            const reg = /^([A-Za-z0-9\u4e00-\u9fa5]){2,20}$/
            if (!reg.test(value)) {
              callback(new Error('Please enter 2-20 characters'))
            } else {
              callback()
            }
          }
        },
        trigger: 'blur'
      },
      idType: {
        required: true,
        message: 'Please select a category',
        trigger: 'change'
      },
      image: {
        required: true,
        message: 'Image cannot be empty'
      },
      price: {
        required: true,
        // 'message': '请输入套餐价格',
        validator: (rules: any, value: string, callback: Function) => {
          const reg = /^([1-9]\d{0,5}|0)(\.\d{1,2})?$/
          if (!reg.test(value) || Number(value) <= 0) {
            callback(
              new Error(
                'Greater than zero and with up to two decimal places'
              )
            )
          } else {
            callback()
          }
        },
        trigger: 'blur'
      },
      code: { required: true, message: 'Please fill in the product code', trigger: 'blur' }
    }
  }

  created() {
    this.getDishTypeList()
    this.actionType = this.$route.query.id ? 'edit' : 'add'
    if (this.actionType == 'edit') {
      this.init()
    }
  }

  // 删除套餐菜品
  delDishHandle(index: any) {
    this.dishTable.splice(index, 1)
    this.checkList = this.dishTable
    // this.checkList.splice(index, 1)
  }

  // 添加菜品
  openAddDish(st: string) {
    this.seachKey = ''
    this.dialogVisible = true
  }

  // 取消添加菜品
  handleClose(done: any) {
    // this.$refs.adddish.close()
    this.dialogVisible = false
    this.checkList = JSON.parse(JSON.stringify(this.dishTable))
    // this.dialogVisible = false
  }

  // 通过套餐ID获取菜品列表分类
  // private getDishList (id:number) {
  //   getDishListType({id}).then(res => {
  //     if (res.data.code == 200) {
  //       const { data } = res.data
  //       this.dishList = data
  //     } else {
  //       this.$message.error(res.data.desc)
  //     }
  //   })
  // }

  // 保存添加菜品列表
  public addTableList() {
    this.dishTable = JSON.parse(JSON.stringify(this.checkList))
    this.dishTable.forEach((n: any) => {
      n.copies = 1
    })
    this.dialogVisible = false
  }

  public submitForm(formName: any, st: any) {
    ;(this.$refs[formName] as any).validate((valid: any) => {
      if (valid) {
        if (this.dishTable.length === 0) {
          return this.$message.error('Dish cannot be empty')
        }
        if (!this.ruleForm.image) return this.$message.error('Image cannot be empty')
        let prams = { ...this.ruleForm } as any
        prams.setmealDishes = this.dishTable.map((obj: any) => ({
          copies: obj.copies,
          dishId: obj.dishId,
          name: obj.name,
          price: obj.price
        }))
        ;(prams as any).status =
          this.actionType === 'add' ? 0 : this.ruleForm.status ? 1 : 0
        prams.categoryId = this.ruleForm.idType
        // delete prams.dishList
        if (this.actionType == 'add') {
          delete prams.id
          addSetmeal(prams)
            .then(res => {
              if (res && res.data && res.data.code === 1) {
                this.$message.success('Success!')
                if (!st) {
                  this.$router.push({ path: '/setmeal' })
                } else {
                  ;(this as any).$refs.ruleForm.resetFields()
                  this.dishList = []
                  this.dishTable = []
                  this.ruleForm = {
                    name: '',
                    categoryId: '',
                    price: '',
                    code: '',
                    image: '',
                    description: '',
                    dishList: [],
                    status: true,
                    id: '',
                    idType: ''
                  } as any
                  this.imageUrl = ''
                }
              } else {
                this.$message.error(res.data.msg)
              }
            })
            .catch(err => {
              this.$message.error('error:' + err.message)
            })
        } else {
          delete prams.updateTime
          editSetmeal(prams)
            .then(res => {
              if (res.data.code === 1) {
                this.$message.success('Success!')
                this.$router.push({ path: '/setmeal' })
              } else {
                // this.$message.error(res.data.desc || res.data.message)
              }
            })
            .catch(err => {
              this.$message.error('error：' + err.message)
            })
        }
      } else {
        // console.log('error submit!!')
        return false
      }
    })
  }

  imageChange(value: any) {
    this.ruleForm.image = value
  }

  private async init() {
    querySetmealById(this.$route.query.id).then(res => {
      if (res && res.data && res.data.code === 1) {
        this.ruleForm = res.data.data
        this.ruleForm.status = res.data.data.status == '1'
        ;(this.ruleForm as any).price = res.data.data.price
        // this.imageUrl = `http://172.17.2.120:8080/common/download?name=${res.data.data.image}`
        this.imageUrl = res.data.data.image
        this.checkList = res.data.data.setmealDishes
        this.dishTable = res.data.data.setmealDishes.reverse()
        this.ruleForm.idType = res.data.data.categoryId
      } else {
        this.$message.error(res.data.msg)
      }
    })
  }

  private seachHandle() {
    this.seachKey = this.value
  }

  // 获取套餐分类
  private getDishTypeList() {
    getCategoryList({ type: 2, page: 1, pageSize: 1000 }).then(res => {
      if (res && res.data && res.data.code === 1) {
        this.setMealList = res.data.data.map((obj: any) => ({
          ...obj,
          idType: obj.id
        }))
      } else {
        this.$message.error(res.data.msg)
      }
    })
  }

  // 获取添加菜品数据 - 确定加菜倒序展示
  private getCheckList(value: any) {
    this.checkList = [...value].reverse()
  }
}
</script>
<style>
.avatar-uploader .el-icon-plus:after {
  position: absolute;
  display: inline-block;
  content: ' ' !important;
  left: calc(50% - 20px);
  top: calc(50% - 40px);
  width: 40px;
  height: 40px;
  background: url('./../../assets/icons/icon_upload@2x.png') center center no-repeat;
  background-size: 20px;
}
</style>
<style lang="scss">
// .el-form-item__error {
//   top: 90%;
// }
.addBrand-container {
  .avatar-uploader .el-upload {
    border: 1px dashed #d9d9d9;
    border-radius: 6px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
  }

  .avatar-uploader .el-upload:hover {
    border-color: #ffc200;
  }

  .avatar-uploader-icon {
    font-size: 28px;
    color: #8c939d;
    width: 200px;
    height: 160px;
    line-height: 160px;
    text-align: center;
  }

  .avatar {
    width: 200px;
    height: 160px;
    display: block;
  }

  // .el-form--inline .el-form-item__content {
  //   width: 293px;
  // }

  .el-input {
    width: 293px;
  }

  .address {
    .el-form-item__content {
      width: 777px !important;
    }
  }

  .el-input__prefix {
    top: 2px;
  }

  .addDish {
    .el-input {
      width: 130px;
    }

    .el-input-number__increase {
      border-left: solid 1px #fbe396;
      background: #fffbf0;
    }

    .el-input-number__decrease {
      border-right: solid 1px #fbe396;
      background: #fffbf0;
    }

    input {
      border: 1px solid #fbe396;
    }

    .table {
      border: solid 1px #ebeef5;
      border-radius: 3px;

      th {
        padding: 5px 0;
      }

      td {
        padding: 7px 0;
      }
    }
  }

  .addDishList {
    .seachDish {
      position: absolute;
      top: 12px;
      right: 20px;
    }

    .el-dialog__footer {
      padding-top: 27px;
    }

    .el-dialog__body {
      padding: 0;
      border-bottom: solid 1px #efefef;
    }

    .seachDish {
      .el-input__inner {
        height: 40px;
        line-height: 40px;
      }
    }
  }
}
</style>
<style lang="scss" scoped>
.addBrand {
  &-container {
    margin: 30px;

    .container {
      position: relative;
      z-index: 1;
      background: #fff;
      padding: 30px;
      border-radius: 4px;
      min-height: 500px;

      .subBox {
        padding-top: 30px;
        text-align: center;
        border-top: solid 1px $gray-5;
      }

      .el-input {
        width: 350px;
      }

      .addDish {
        width: 777px;

        .addBut {
          background: #ffc200;
          display: inline-block;
          padding: 0px 20px;
          border-radius: 3px;
          line-height: 40px;
          cursor: pointer;
          border-radius: 4px;
          color: #333333;
          font-weight: 500;
        }

        .content {
          background: #fafafb;
          padding: 20px;
          border: solid 1px #d8dde3;
          border-radius: 3px;
        }
      }
    }
  }
}
</style>
