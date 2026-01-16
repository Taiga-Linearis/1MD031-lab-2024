<template>
  <div class="burger-card-inner">
    <h3>{{ burger.name }}</h3>
    <img :src="burger.img" :alt="burger.name" width="300" height="200" />
    <ul>
      <li>Delicious house-made patty</li>
      <li v-if="burger.lactose" class="lactose">Contains lactose</li>
      <li v-if="burger.gluten" class="lactose">Contains gluten</li>
      <li>{{ burger.kCal }} kCal</li>
    </ul>

    <div class="order-controls">
      <button type="button" @click="decrease">-</button>
      <span class="amount"> {{ amountOrdered }} </span>
      <button type="button" @click="increase">+</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'OneBurger',
  props: {
    burger: Object
  },
  data() {
    return {
      amountOrdered: 0
    }
  },
  methods: {
    increase() {
      this.amountOrdered++
    },
    decrease() {
      if (this.amountOrdered > 0) this.amountOrdered--
    }
  }
}
</script>

<style scoped>

.burger-card-inner img {
  display: block;
  margin-bottom: 8px;
}

.order-controls {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-top: 8px;
}
.order-controls button {
  width: 32px;
  height: 32px;
  font-size: 18px;
}
.amount {
  min-width: 24px;
  text-align: center;
}
</style>