<!--  -->
<template>
  <div class="input-auto-complete">
    <el-input
      v-model="input"
      :placeholder="placeholder"
      clearable
      style="width: 250px"
      @clear="init"
      @keyup.enter.native="init"
    >
      <i
        slot="prefix"
        class="el-input__icon el-icon-search"
        style="cursor: pointer"
        @click="init"
      />
    </el-input>
  </div>
</template>

<script lang='ts'>
import { Component, Prop, Vue } from 'vue-property-decorator'

@Component({
  name: 'InputAutoComplete'
})
export default class extends Vue {
  @Prop({ default: [] }) data: Array<any>
  @Prop({ default: '' }) placeholder: string
  @Prop({ default: 'name' }) ObKey: string
  private input: any = ''

  init() {
    this.$emit('init', this.input)
  }
}
</script>
<style scoped>
.input-auto-complete {
  display: inline-block;
}
</style>
