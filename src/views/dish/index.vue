<template>
  <div class="dashboard-container">
    <div class="container">
      <div class="tableBar">
        <label style="margin-right: 10px">Name:</label>
        <el-input v-model="input"
                  clearable
                  style="width: 14%"
                  @clear="init"
                  @keyup.enter.native="initFun" />

        <label style="margin-right: 10px; margin-left: 20px">Category:</label>
        <el-select v-model="categoryId"
                   clearable
                   style="width: 14%"
                   @clear="init">
          <el-option v-for="item in dishCategoryList"
                     :key="item.value"
                     :label="item.label"
                     :value="item.value" />
        </el-select>

        <label style="margin-right: 10px; margin-left: 20px">Status:</label>
        <el-select v-model="dishStatus"
                   clearable
                   style="width: 14%"
                   @clear="init">
          <el-option v-for="item in saleStatus"
                     :key="item.value"
                     :label="item.label"
                     :value="item.value" />
        </el-select>
        <el-button class="normal-btn continue"
                   @click="init(true)">
          Select
        </el-button>

        <div class="tableLab">
          <span class="delBut non"
                @click="deleteHandle('batch', null)">Batch Delete</span>
          <!-- <span class="blueBug non" @click="statusHandle('1')">批量启售</span>
          <span
            style="border: none"
            class="delBut non"
            @click="statusHandle('0')"
            >批量停售</span
          > -->
          <el-button style="margin-left: 15px"
                     type="primary"
                     @click="addDishtype('add')">
            + Add Dish
          </el-button>
        </div>
      </div>
      <el-table v-if="tableData.length"
                :data="tableData"
                class="tableBox"
                stripe
                @selection-change="handleSelectionChange">
        <el-table-column type="selection"
                         width="25" />
        <el-table-column label="Name"
                         prop="name" />
        <el-table-column label="Image"
                         prop="image">
          <template slot-scope="{ row }">
            <el-image :src="row.image"
                      style="width: 80px; height: 40px; border: none; cursor: pointer">
              <div slot="error"
                   class="image-slot">
                <img src="./../../assets/noImg.png"
                     style="width: auto; height: 40px; border: none">
              </div>
            </el-image>
          </template>
        </el-table-column>
        <el-table-column label="Category"
                         prop="categoryName" />
        <el-table-column label="Price">
          <template slot-scope="scope">
            <span style="margin-right: 10px">￥{{ (scope.row.price).toFixed(2) * 100 / 100 }}</span>
          </template>
        </el-table-column>
        <el-table-column label="Status">
          <template slot-scope="scope">
            <div :class="{ 'stop-use': String(scope.row.status) === '0' }"
                 class="tableColumn-status">
              {{ String(scope.row.status) === '0' ? 'Disabled' : 'Enabled' }}
            </div>
          </template>
        </el-table-column>
        <el-table-column label="Last Operation Time"
                         prop="updateTime"
                         width="200" />
        <el-table-column align="center"
                         label="Operate"
                         width="250">
          <template slot-scope="scope">
            <el-button class="blueBug"
                       size="small"
                       type="text"
                       @click="addDishtype(scope.row.id)">
              Update
            </el-button>
            <el-button class="delBut"
                       size="small"
                       type="text"
                       @click="deleteHandle('delete', scope.row.id)">
              Delete
            </el-button>
            <el-button :class="{
                         blueBug: scope.row.status == '0',
                         delBut: scope.row.status != '0'
                       }"
                       class="non"
                       size="small"
                       type="text"
                       @click="statusHandle(scope.row)">
              {{ scope.row.status == '0' ? 'Enable' : 'Disable' }}
            </el-button>
          </template>
        </el-table-column>
      </el-table>
      <Empty v-else
             :is-search="isSearch" />
      <el-pagination v-if="counts > 10"
                     :page-size="pageSize"
                     :page-sizes="[10, 20, 30, 40]"
                     :total="counts"
                     class="pageList"
                     layout="total, sizes, prev, pager, next, jumper"
                     @size-change="handleSizeChange"
                     @current-change="handleCurrentChange" />
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import HeadLable from '@/components/HeadLable/index.vue'
import { deleteDish, dishCategoryList, dishStatusByStatus, getDishPage } from '@/api/dish'
import InputAutoComplete from '@/components/InputAutoComplete/index.vue'
import Empty from '@/components/Empty/index.vue'

@Component({
  name: 'DishType',
  components: {
    HeadLable,
    InputAutoComplete,
    Empty
  }
})
export default class extends Vue {
  private input: any = ''
  private counts: number = 0
  private page: number = 1
  private pageSize: number = 10
  private checkList: string[] = []
  private tableData: [] = []
  private dishState = ''
  private dishCategoryList = []
  private categoryId = ''
  private dishStatus = ''
  private isSearch: boolean = false
  private saleStatus: any = [
    {
      value: 0,
      label: 'Disabled'
    },
    {
      value: 1,
      label: 'Enabled'
    }
  ]

  created() {
    this.init()
    this.getDishCategoryList()
  }

  initProp(val) {
    this.input = val
    this.initFun()
  }

  initFun() {
    this.page = 1
    this.init()
  }

  private async init(isSearch?) {
    this.isSearch = isSearch
    await getDishPage({
      page: this.page,
      pageSize: this.pageSize,
      name: this.input || undefined,
      categoryId: this.categoryId || undefined,
      status: this.dishStatus
    })
      .then(res => {
        if (res.data.code === 1) {
          this.tableData = res.data && res.data.data && res.data.data.records
          this.counts = Number(res.data.data.total)
        }
      })
      .catch(err => {
        this.$message.error('error:' + err.message)
      })
  }

  // 添加
  private addDishtype(st: string) {
    if (st === 'add') {
      this.$router.push({ path: '/dish/add' })
    } else {
      this.$router.push({ path: '/dish/add', query: { id: st } })
    }
  }

  // 删除
  private deleteHandle(type: string, id: any) {
    if (type === 'batch' && id === null) {
      if (this.checkList.length === 0) {
        return this.$message.error('Please select the dish to delete!')
      }
    }
    this.$confirm('Confirm?', 'Confirm', {
      confirmButtonText: 'Yes',
      cancelButtonText: 'No',
      type: 'warning'
    }).then(() => {
      deleteDish(type === 'batch' ? this.checkList.join(',') : id)
        .then(res => {
          if (res && res.data && res.data.code === 1) {
            this.$message.success('Success!')
            this.init()
          } else {
            this.$message.error(res.data.msg)
          }
        })
        .catch(err => {
          this.$message.error('error:' + err.message)
        })
    })
  }

  //获取菜品分类下拉数据
  private getDishCategoryList() {
    dishCategoryList({
      type: 1
    })
      .then(res => {
        if (res && res.data && res.data.code === 1) {
          this.dishCategoryList = (
            res.data &&
            res.data.data &&
            res.data.data
          ).map(item => {
            return { value: item.id, label: item.name }
          })
        }
      })
      .catch(() => {
      })
  }

  //状态更改
  private statusHandle(row: any) {
    let params: any = {}
    if (typeof row === 'string') {
      if (this.checkList.length === 0) {
        this.$message.error('Please select the dish first!')
        return false
      }
      params.id = this.checkList.join(',')
      params.status = row
    } else {
      params.id = row.id
      params.status = row.status ? '0' : '1'
    }
    this.dishState = params
    this.$confirm('Confirm?', 'Confirm', {
      confirmButtonText: 'Yes',
      cancelButtonText: 'No',
      type: 'warning'
    }).then(() => {
      // 起售停售---批量起售停售接口
      dishStatusByStatus(this.dishState)
        .then(res => {
          if (res && res.data && res.data.code === 1) {
            this.$message.success('Success!')
            this.init()
          } else {
            this.$message.error(res.data.msg)
          }
        })
        .catch(err => {
          this.$message.error('error：' + err.message)
        })
    })
  }

  // 全部操作
  private handleSelectionChange(val: any) {
    let checkArr: any[] = []
    val.forEach((n: any) => {
      checkArr.push(n.id)
    })
    this.checkList = checkArr
  }

  private handleSizeChange(val: any) {
    this.pageSize = val
    this.init()
  }

  private handleCurrentChange(val: any) {
    this.page = val
    this.init()
  }
}
</script>
<style lang="scss">
.el-table-column--selection .cell {
  padding-left: 10px;
}
</style>
<style lang="scss" scoped>
.dashboard {
  &-container {
    margin: 30px;

    .container {
      background: #fff;
      position: relative;
      z-index: 1;
      padding: 30px 28px;
      border-radius: 4px;
      //查询黑色按钮样式
      .normal-btn {
        background: #333333;
        color: white;
        margin-left: 20px;
      }

      .tableBar {
        margin-bottom: 20px;

        .tableLab {
          display: inline-block;
          float: right;

          span {
            cursor: pointer;
            display: inline-block;
            font-size: 14px;
            padding: 0 20px;
            color: $gray-2;
          }
        }
      }

      .tableBox {
        width: 100%;
        border: 1px solid $gray-5;
        border-bottom: 0;
      }

      .pageList {
        text-align: center;
        margin-top: 30px;
      }
    }
  }
}
</style>
