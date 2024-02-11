<template>
  <div class="container">
    <h2 class="homeTitle">Data Overview</h2>
    <div class="overviewBox">
      <ul>
        <li>
          <p class="tit">Turnover</p>
          <p class="num">{{ overviewData.turnover }}</p>
          <p class="tip">
            Growth<span v-if="overviewData.turnoverGrowth">
              <span
                :class="overviewData.turnoverGrowth > 0 ? '' : 'green'"
                class="red"
              >{{
                  overviewData.turnoverGrowth > 0
                    ? '+' + overviewData.turnoverGrowth
                    : overviewData.turnoverGrowth
                }}%</span
              >
            </span>
            <span v-else>-</span>
          </p>
        </li>
        <li>
          <p class="tit">Valid orders</p>
          <p class="num">{{ overviewData.validOrderCount }}</p>
          <p class="tip">
            Growth<span v-if="overviewData.validOrderCountGrowth">
              <span
                :class="overviewData.validOrderCountGrowth > 0 ? '' : 'green'"
                class="red"
              >{{
                  overviewData.validOrderCountGrowth > 0
                    ? '+' + overviewData.validOrderCountGrowth
                    : overviewData.validOrderCountGrowth
                }}%</span
              >
            </span>
            <span v-else>-</span>
          </p>
        </li>
        <li>
          <p class="tit">Order completion rate</p>
          <p class="num">
            {{ (overviewData.orderCompletionRate * 100).toFixed(2) }}%
          </p>
          <p class="tip">
            Growth<span v-if="overviewData.orderCompletionRateGrowth">
              <span
                :class="
                  overviewData.orderCompletionRateGrowth > 0 ? '' : 'green'
                "
                class="red"
              >{{
                  overviewData.orderCompletionRateGrowth > 0
                    ? '+' + overviewData.orderCompletionRateGrowth
                    : overviewData.orderCompletionRateGrowth
                }}%</span
              >
            </span>
            <span v-else>-</span>
          </p>
        </li>
        <li>
          <p class="tit">Average Customer Spending</p>
          <p class="num">{{ overviewData.unitPrice.toFixed(2) }}</p>
          <p class="tip">
            Growth<span v-if="overviewData.unitPriceGrowth">
              <span
                :class="overviewData.unitPriceGrowth > 0 ? '' : 'green'"
                class="red"
              >{{
                  overviewData.unitPriceGrowth > 0
                    ? '+' + overviewData.unitPriceGrowth
                    : overviewData.unitPriceGrowth
                }}%</span
              >
            </span>
            <span v-else>-</span>
          </p>
        </li>
        <li>
          <p class="tit">Total number of users</p>
          <p class="num">{{ overviewData.totalUsers }}</p>
          <!-- <p class="tip">
            同比增长：<span v-if="overviewData.newUsersGrowth">
              <span
                class="red"
                :class="overviewData.newUsersGrowth > 0 ? '' : 'green'"
                >{{
                  overviewData.newUsersGrowth > 0
                    ? '+' + overviewData.newUsersGrowth
                    : overviewData.newUsersGrowth
                }}</span
              ></span
            >
            <span v-else>-</span>
          </p> -->
        </li>
        <li>
          <p class="tit">New users</p>
          <p class="num">{{ overviewData.newUsers }}</p>
          <p class="tip">
            Growth<span v-if="overviewData.newUsersGrowth">
              <span
                :class="overviewData.newUsersGrowth > 0 ? '' : 'green'"
                class="red"
              >{{
                  overviewData.newUsersGrowth > 0
                    ? '+' + overviewData.newUsersGrowth
                    : overviewData.newUsersGrowth
                }}%</span
              >
            </span>
            <span v-else>-</span>
          </p>
        </li>
      </ul>
    </div>
  </div>
</template>
<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator'
import { getday } from '@/utils/formValidate'

@Component({
  name: 'Overview'
})
export default class extends Vue {
  @Prop() private overviewData!: any

  get days() {
    return getday()
  }
}
</script>
