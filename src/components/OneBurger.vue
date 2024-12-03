<template>
  <div class="burger-card">
    <h3>{{ burger.name }}</h3>
    <img :src="burger.imageUrl"class="burger-image" style="width: 100%;" />
    <ul>
      <li>{{ burger.kCal }} kCal</li>
      <li v-if="burger.hasLactose">Contains <span class="ingredient">lactose</span></li>
      <li v-else>Lactose free</li>
      <li v-if="burger.hasGluten">Contains <span class="ingredient">gluten</span></li>
      <li v-else>Gluten free</li>
      
    </ul>
    <div class="order-controls">
      <span>Amount:</span>
      <button @click="decreaseAmount" :disabled="amountOrdered <= 0">-</button>
      <span> {{ amountOrdered }}</span>
      <button @click="increaseAmount">+</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'OneBurger',
  props: {
    burger: Object
  },
  data: function () {
    return {
      amountOrdered: 0 
    }
  },
  methods: {
    increaseAmount() {
      this.amountOrdered += 1;
      this.$emit('orderedBurger', { name: this.burger.name, amount: this.amountOrdered });
    },
    decreaseAmount() {
      if (this.amountOrdered > 0) {
        this.amountOrdered -= 1;
        this.$emit('orderedBurger', { name: this.burger.name, amount: this.amountOrdered });
      }
    }
  },
 
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.burger-card {
  background-color: black;
  color: #fff;
  border-radius: 5px;
  padding: 20px;
  font-size: 110%;
}
.ingredient {
  font-weight: bold;
}
.wrapper {
    display: grid;
    grid-gap: 10px;
    grid-template-columns: repeat(3, 1fr); /* Change to 3 equal columns */
    background-color: black;
    color: #444;
}
#burger-list {
    background-color: black;
    color:white;
    padding: 20px;
    border: 2px dashed white;
    border-radius: 5px;
}
#order-info{
    border:2px dashed black;
    padding: 20px;
}
.burger-image {
  width: 150px; 
  height: 350px; 
  object-fit: cover;
  border-radius: 10px; 
  display: block; 
}

</style>