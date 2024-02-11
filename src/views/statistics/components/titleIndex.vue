<template>
  <div class="title-index">
    <div class="month">
      <ul class="tabs">
        <li
          v-for="(item, index) in tabsParam"
          :key="index"
          :class="{ active: index === nowIndex }"
          class="li-tab"
          @click="toggleTabs(index)"
        >
          {{ item }}
          <span></span>
        </li>
      </ul>
    </div>
    <div class="get-time">
      <p>
        From：{{ tateData[0] }} To
        {{ tateData[tateData.length - 1] }}
      </p>
    </div>
    <el-button
      class="right-el-button"
      icon="iconfont icon-download"
      @click="handleExport"
    >数据导出
    </el-button
    >
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue, Watch } from 'vue-property-decorator'
import { exportInfor } from '@/api/index'

@Component({
  name: 'TitleIndex'
})
export default class extends Vue {
  nowIndex = 2 - 1
  value = []
  tabsParam = ['Yesterday', '7 days', '30 days', 'Week', 'Month']
  @Prop() private flag!: any
  @Prop() private tateData!: any
  @Prop() private turnoverData!: any

  @Watch('flag')
  getNowIndex(val) {
    this.nowIndex = val
  }

  // tab切换
  toggleTabs(index: number) {
    this.nowIndex = index
    this.value = []
    this.$emit('sendTitleInd', index + 1)
  }

  //  数据导出
  /** 导出按钮操作 */
  handleExport() {
    this.$confirm('是否确认导出最近30天运营数据?', '提示', {
      confirmButtonText: '确定',
      cancelButtonText: 'Cancel',
      type: 'warning'
    })
      .then(async function() {
        const { data } = await exportInfor()
        let url = window.URL.createObjectURL(data)
        var a = document.createElement('a')
        document.body.appendChild(a)
        a.href = url
        a.download = '运营数据统计报表.xlsx'
        a.click()
        window.URL.revokeObjectURL(url)
      })
      .then((response) => {
      })
  }
}
</script>
