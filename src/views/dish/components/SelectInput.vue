<template>
  <div class="selectInput">
    <el-input
      v-model="value"
      clearable
      placeholder="Select Flavor"
      readonly
      style="width: 100%"
      type="text"
      @blur="outSelect(false)"
      @focus="selectFlavor(true)"
    />
    <div v-if="mak && dishFlavorsData.length" class="flavorSelect">
      <span
        v-for="(it, ind) in dishFlavorsData"
        :key="ind"
        class="items"
        @click="checkOption(it, ind)"
      >{{ it.name }}</span
      >
      <span v-if="dishFlavorsData == []" class="none">No Data</span>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator'

@Component({
  name: 'selectInput'
})
export default class extends Vue {
  @Prop({ default: [] }) private selectFlavorsData!: []
  @Prop({ default: [] }) private dishFlavorsData!: []
  @Prop({ default: '' }) private value!: number
  @Prop({ default: 0 }) private index!: number
  private keyValue = NaN

  private mak: boolean = false

  checkOption(val: any, ind: any) {
    this.$emit('select', val.name, this.index, ind)
    this.keyValue = val.name
  }

  private selectFlavor(st: boolean) {
    this.mak = st
  }

  private outSelect(st: boolean) {
    const _this = this
    setTimeout(function() {
      _this.mak = st
    }, 200)
  }

  private inputHandle(val: any) {
    this.selectFlavor(false)
  }
}
</script>

<style lang="scss" scoped>
.selectInput {
  position: relative;
  width: 100%;
  min-width: 100px;

  .flavorSelect {
    position: absolute;
    width: 100%;
    // padding: 0 10px;
    border-radius: 3px;
    border: solid 1px #e4e7ed;
    line-height: 30px;
    text-align: center;
    background: #fff;
    top: 50px;
    z-index: 99;

    .items {
      cursor: pointer;
      display: inline-block;
      width: 100%;
      line-height: 35px;
      border-bottom: solid 1px #f4f4f4;
      color: #666;
      margin: 0 !important;

      &:hover {
        background-color: #fffbf0;
      }

      &:active {
        background-color: #fffbf0;
        color: #ffc200;
      }
    }

    .none {
      font-size: 14px;
    }
  }
}
</style>
