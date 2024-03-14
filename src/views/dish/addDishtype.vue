<template>
  <div :key="vueRest"
       class="addBrand-container">
    <div :key="restKey"
         class="container">
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
                        prop="categoryId">
            <el-select v-model="ruleForm.categoryId"
                       placeholder="Please enter the category">
              <el-option v-for="(item, index) in dishList"
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
        <el-form-item label="Flavor:">
          <el-form-item>
            <div class="flavorBox">
              <span v-if="dishFlavors.length == 0"
                    class="addBut"
                    @click="addFlavore">
                + Add Flavor</span>
              <div v-if="dishFlavors.length != 0"
                   class="flavor">
                <div class="title">
                  <span>Flavor</span>
                  <!-- <span class="des-box">口味标签（输入标签回车添加）</span> -->
                </div>
                <div class="cont">
                  <div v-for="(item, index) in dishFlavors"
                       :key="index"
                       class="items">
                    <div class="itTit">
                      <!-- :dish-flavors-data="filterDishFlavorsData()" -->
                      <SelectInput :dish-flavors-data="leftDishFlavors"
                                   :index="index"
                                   :value="item.name"
                                   @select="selectHandle" />
                    </div>
                    <div class="labItems"
                         style="display: flex">
                      <span v-for="(it, ind) in item.value"
                            :key="ind">{{ it }}
                        <i @click="delFlavorLabel(index, ind)">X</i></span>
                      <div :style="inputStyle"
                           class="inputBox" />
                    </div>
                    <span class="delFlavor delBut non"
                          @click="delFlavor(item.name)">Delete</span>
                  </div>
                </div>
                <div v-if="
                       !!this.leftDishFlavors.length &&
                         this.dishFlavors.length < this.dishFlavorsData.length
                     "
                     class="addBut"
                     @click="addFlavore">
                  Add Flavor
                </div>
              </div>
            </div>
          </el-form-item>
        </el-form-item>
        <div>
          <el-form-item label="Image:"
                        prop="image">
            <image-upload :prop-image-url="imageUrl"
                          @imageChange="imageChange">
              Image size not more than 2M<br>Only PNG JPEG JPG type images can be uploaded<br>It is recommended to
              upload a 200*200 or 300*300 size image
            </image-upload>
          </el-form-item>
        </div>
        <div class="address">
          <el-form-item label="Description:"
                        prop="region">
            <el-input v-model="ruleForm.description"
                      :rows="3"
                      maxlength="200"
                      placeholder="Description of the dish, maximum 200 words"
                      type="textarea" />
          </el-form-item>
        </div>
        <div class="subBox address">
          <el-button @click="() => $router.back()">
            Cancel
          </el-button>
          <el-button :class="{ continue: actionType === 'add' }"
                     type="primary"
                     @click="submitForm('ruleForm')">
            Submit
          </el-button>
          <el-button v-if="actionType == 'add'"
                     type="primary"
                     @click="submitForm('ruleForm', 'goAnd')">
            Submit and continue adding
          </el-button>
        </div>
      </el-form>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Watch } from 'vue-property-decorator'
import HeadLable from '@/components/HeadLable/index.vue'
import SelectInput from './components/SelectInput.vue'
import ImageUpload from '@/components/ImgUpload/index.vue'
// getFlavorList口味列表暂时不做 getDishTypeList
import { addDish, editDish, getCategoryList, queryDishById } from '@/api/dish'
import { getToken } from '@/utils/cookies'

@Component({
  name: 'addShop',
  components: {
    HeadLable,
    SelectInput,
    ImageUpload
  }
})
export default class extends Vue {
  private restKey: number = 0
  private textarea: string = ''
  private value: string = ''
  private imageUrl: string = ''
  private actionType: string = ''
  private dishList: string[] = []
  private dishFlavorsData: any[] = [] //原始口味数据
  private dishFlavors: any[] = [] //待上传口味的数据
  private leftDishFlavors: any[] = [] //下拉框剩余可选择的口味数据
  private vueRest = '1'
  private index = 0
  private inputStyle = { flex: 1 }
  private headers = {
    token: getToken()
  }
  private ruleForm = {
    name: '',
    id: '',
    price: '',
    code: '',
    image: '',
    description: '',
    dishFlavors: [],
    status: true,
    categoryId: ''
  }

  get rules() {
    return {
      name: [
        {
          required: true,
          validator: (rule: any, value: string, callback: Function) => {
            if (!value) {
              callback(new Error('Please enter the name'))
            }
              else if (value.length < 2) {
              callback(new Error('Please enter 2-20 characters'))
            }
            else{
              callback()

            }
          },
          trigger: 'blur'
        }
      ],
      categoryId: [
        { required: true, message: 'Please select a category', trigger: 'change' }
      ],
      image: {
        required: true,
        message: 'Image cannot be empty'
      },
      price: [
        {
          required: true,
          // 'message': '请填写菜品价格',
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
        }
      ],
      code: [{ required: true, message: 'Please fill in the product code', trigger: 'blur' }]
    }
  }

  created() {
    this.getDishList()
    // 口味临时数据
    this.getFlavorListHand()
    this.actionType = this.$route.query.id ? 'edit' : 'add'
    if (this.$route.query.id) {
      this.init()
    }
  }

  mounted() {
  }

  @Watch('dishFlavors')
  changeDishFlavors() {
    this.getLeftDishFlavors()
  }

  //过滤已选择的口味下拉框无法再次选择
  getLeftDishFlavors() {
    let arr = []
    this.dishFlavorsData.map(item => {
      if (
        this.dishFlavors.findIndex(item1 => item.name === item1.name) === -1
      ) {
        arr.push(item)
      }
    })
    this.leftDishFlavors = arr
  }

  imageChange(value: any) {
    this.ruleForm.image = value
  }

  private selectHandle(val: any, key: any, ind: any) {
    const arrDate = [...this.dishFlavors]
    const index = this.dishFlavorsData.findIndex(item => item.name === val)
    arrDate[key] = JSON.parse(JSON.stringify(this.dishFlavorsData[index]))
    this.dishFlavors = arrDate
  }

  private async init() {
    queryDishById(this.$route.query.id).then(res => {
      if (res && res.data && res.data.code === 1) {
        this.ruleForm = { ...res.data.data }
        this.ruleForm.price = String(res.data.data.price)
        this.ruleForm.status = res.data.data.status == '1'
        this.dishFlavors =
          res.data.data.flavors &&
          res.data.data.flavors.map(obj => ({
            ...obj,
            value: JSON.parse(obj.value)
          }))
        let arr = []
        this.getLeftDishFlavors()
        this.imageUrl = res.data.data.image
      } else {
        this.$message.error(res.data.msg)
      }
    })
  }

  // 按钮 - 添加口味
  private addFlavore() {
    this.dishFlavors.push({ name: '', value: [] }) // JSON.parse(JSON.stringify(this.dishFlavorsData))
  }

  // 按钮 - 删除口味
  private delFlavor(name: string) {
    let ind = this.dishFlavors.findIndex(item => item.name === name)
    this.dishFlavors.splice(ind, 1)
  }

  // 按钮 - 删除口味标签
  private delFlavorLabel(index: number, ind: number) {
    this.dishFlavors[index].value.splice(ind, 1)
  }

  //口味位置记录
  private flavorPosition(index: number) {
    this.index = index
  }

  // 添加口味标签
  private keyDownHandle(val: any) {
    if (event) {
      event.cancelBubble = true
      event.preventDefault()
      event.stopPropagation()
    }

    if (val.target.innerText.trim() != '') {
      this.dishFlavors[this.index].flavorData.push(val.target.innerText)
      val.target.innerText = ''
    }
  }

  // 获取菜品分类
  private getDishList() {
    getCategoryList({ type: 1 }).then(res => {
      if (res.data.code === 1) {
        this.dishList = res && res.data && res.data.data
      } else {
        this.$message.error(res.data.msg)
      }
      // if (res.data.code == 200) {
      //   const {data} = res.data
      //   this.dishList = data
      // } else {
      //   this.$message.error(res.data.desc)
      // }
    })
  }

  // 获取口味列表
  private getFlavorListHand() {
    // flavor flavorData
    this.dishFlavorsData = [
      { name: 'Sweetness', value: ['No Sugar', 'Less Sugar', 'Half Sugar', 'More Sugar', 'Full Sugar'] },
      { name: 'Temperature', value: ['Hot', 'Normal', 'No Ice', 'Less Ice', 'More Ice'] },
      { name: 'Diet', value: ['No Scallions', 'No Garlic', 'No Cilantro'] },
      { name: 'Spiciness', value: ['No Spicy', 'Less Spicy', 'Normal Spicy'] }
    ]
  }

  private submitForm(formName: any, st: any) {
    ;(this.$refs[formName] as any).validate((valid: any) => {
      console.log(valid, 'valid')
      if (valid) {
        if (!this.ruleForm.image) return this.$message.error('Image cannot be empty')
        let params: any = { ...this.ruleForm }
        // params.flavors = this.dishFlavors
        params.status =
          this.actionType === 'add' ? 1 : this.ruleForm.status ? 1 : 0
        // params.price *= 100
        params.categoryId = this.ruleForm.categoryId
        params.flavors = this.dishFlavors.map(obj => ({
          ...obj,
          value: JSON.stringify(obj.value)
        }))
        delete params.dishFlavors
        if (this.actionType == 'add') {
          delete params.id
          addDish(params)
            .then(res => {
              if (res.data.code === 1) {
                this.$message.success('Success!')
                if (!st) {
                  this.$router.push({ path: '/dish' })
                } else {
                  this.dishFlavors = []
                  // this.dishFlavorsData = []
                  this.imageUrl = ''
                  this.ruleForm = {
                    name: '',
                    id: '',
                    price: '',
                    code: '',
                    image: '',
                    description: '',
                    dishFlavors: [],
                    status: true,
                    categoryId: ''
                  }
                  this.restKey++
                }
              } else {
                this.$message.error(res.data.desc || res.data.msg)
              }
            })
            .catch(err => {
              this.$message.error('error:' + err.message)
            })
        } else {
          delete params.createTime
          delete params.updateTime
          editDish(params)
            .then(res => {
              if (res && res.data && res.data.code === 1) {
                this.$router.push({ path: '/dish' })
                this.$message.success('Success!')
              } else {
                this.$message.error(res.data.desc || res.data.msg)
              }
              // if (res.data.code == 200) {
              //   this.$router.push({'path': '/dish'})
              //   this.$message.success('菜品修改成功！')
              // } else {
              //   this.$message.error(res.data.desc || res.data.message)
              // }
            })
            .catch(err => {
              this.$message.error('error:' + err.message)
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
.addBrand-container {
  .el-form--inline .el-form-item__content {
    width: 293px;
  }

  .el-input {
    width: 350px;
  }

  .address {
    .el-form-item__content {
      width: 777px !important;
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

      .upload-item {
        .el-form-item__error {
          top: 90%;
        }
      }
    }
  }
}

.flavorBox {
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

  .flavor {
    border: solid 1px #dfe2e8;
    border-radius: 3px;
    padding: 15px;
    background: #fafafb;

    .title {
      color: #606168;

      .des-box {
        padding-left: 44px;
      }
    }

    .cont {
      .items {
        display: flex;
        margin: 10px 0;

        .itTit {
          width: 150px;
          margin-right: 15px;

          input {
            width: 100%;
            // line-height: 40px;
            // border-radius: 3px;
            // padding: 0 10px;
          }
        }

        .labItems {
          flex: 1;
          display: flex;
          flex-wrap: wrap;
          border-radius: 3px;
          min-height: 39px;
          border: solid 1px #d8dde3;
          background: #fff;
          padding: 0 5px;

          span {
            display: inline-block;
            color: #ffc200;
            margin: 5px;
            line-height: 26px;
            padding: 0 10px;
            background: #fffbf0;
            border: 1px solid #fbe396;
            border-radius: 4px;
            font-size: 12px;

            i {
              cursor: pointer;
              font-style: normal;
            }
          }

          .inputBox {
            display: inline-block;
            width: 100%;
            height: 36px;
            line-height: 36px;
            overflow: hidden;
          }
        }

        .delFlavor {
          display: inline-block;
          padding: 0 10px;
          color: #f19c59;
          cursor: pointer;
        }
      }
    }
  }
}
</style>
