<template>
  <div class="container">
    <h2 class="homeTitle">
      Order Management<i>{{ days[1] }}</i
    ><span><router-link to="/order">Order Details</router-link></span>
    </h2>
    <div class="orderviewBox">
      <ul>
        <li>
          <span class="status"
          ><i class="iconfont icon-waiting"></i>Pending Order</span
          >
          <span class="num tip"
          ><router-link to="/order?status=2">{{
              orderviewData.waitingOrders
            }}</router-link></span
          >
        </li>
        <li>
          <span class="status"
          ><i class="iconfont icon-staySway"></i>Awaiting delivery</span
          >
          <span class="num tip"
          ><router-link to="/order?status=3">{{
              orderviewData.deliveredOrders
            }}</router-link></span
          >
        </li>
        <li>
          <span class="status"
          ><i class="iconfont icon-complete"></i>Completed</span
          >
          <span class="num"
          ><router-link to="/order?status=5">{{
              orderviewData.completedOrders
            }}</router-link></span
          >
        </li>
        <li>
          <span class="status"><i class="iconfont icon-cancel"></i>Cancelled</span>
          <span class="num"
          ><router-link to="/order?status=6">{{
              orderviewData.cancelledOrders
            }}</router-link></span
          >
        </li>
        <li>
          <span class="status"><i class="iconfont icon-all"></i>All Orders</span>
          <span class="num"
          ><router-link to="/order">{{
              orderviewData.allOrders
            }}</router-link></span
          >
        </li>
      </ul>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator'
import { getday } from '@/utils/formValidate'

@Component({
  name: 'Orderview'
})
export default class extends Vue {
  @Prop() private orderviewData!: any

  get days() {
    return getday()
  }
}
</script>
