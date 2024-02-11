<template>
  <div class="container">
    <h2 class="homeTitle">
      Dish Overview<span><router-link to="dish">Dish Management</router-link></span>
    </h2>
    <div class="orderviewBox">
      <ul>
        <li>
          <span class="status"><i class="iconfont icon-open"></i>Now on sale</span>
          <span class="num">{{ dishesData.sold }}</span>
        </li>
        <li>
          <span class="status"><i class="iconfont icon-stop"></i>Discontinued</span>
          <span class="num">{{ dishesData.discontinued }}</span>
        </li>
        <li class="add">
          <router-link to="/dish/add">
            <i></i>
            <p>Add Dish</p>
          </router-link>
        </li>
      </ul>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator'
import orderList from './orderList.vue'

@Component({
  components: { orderList },
  name: 'cuisineview'
})
export default class extends Vue {
  @Prop() private dishesData!: any
}
</script>
