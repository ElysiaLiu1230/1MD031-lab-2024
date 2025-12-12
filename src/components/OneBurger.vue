<template>
  <div class="burger">
    <h3>{{ burger.name }}</h3>

    <img :src="burger.img" width="120">

    <ul>
      <li>{{ burger.kCal }} kcal</li>
      <li>
        <span v-if="burger.gluten" class="allergy">
          Contains gluten
        </span>
        <span v-else>
          Gluten free
        </span>
      </li>
      <li>
        <span v-if="burger.lactose" class="allergy">
          Contains lactose
        </span>
        <span v-else>
          Lactose free
        </span>
      </li>
    </ul>

    <p>Ordered: {{ amountOrdered }}</p>

    <button @click="increase">+</button>
    <button @click="decrease">-</button>
  </div>
</template>

<script>
export default {
  name: "OneBurger",
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
      this.$emit("orderedBurger", {
        name: this.burger.name,
        amount: this.amountOrdered
      })
    },

    decrease() {
      if (this.amountOrdered > 0) {
        this.amountOrdered--
      }

      this.$emit("orderedBurger", {
        name: this.burger.name,
        amount: this.amountOrdered
      })
    }
  }
}
</script>

<style scoped>
.burger {
  text-align: left;
}
.allergy {
  font-weight: bold;
}
button {
  margin-right: 8px;
}
</style>
