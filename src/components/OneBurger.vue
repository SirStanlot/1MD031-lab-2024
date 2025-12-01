<template>
  <div class="burger">
    <h4>{{ burger.name }}</h4>
    <p>Amount in order: {{ amountOrdered }}
      <button class="arrow" id="upArrow" v-on:click="incrementOrder"></button>
      <button class="arrow" id="downArrow" v-on:click="decrementOrder"></button>
    </p>
    <img :src="burger.src" class="burgerImage"/>
    <ul>
      <span v-if="burger.george === false">
        <li>{{ burger.kCal }} kCal</li>
        <li v-if="burger.containsGluten">Contains <span class="ingredient">gluten</span></li>
        <li v-else><span class="ingredient">Gluten</span> free</li>

        <li v-if="burger.containsLactose">Contains <span class="ingredient">lactose</span></li>
        <li v-else><span class="ingredient">Lactose</span> free</li>
      </span>

      <li>{{ burger.extraLines }}</li>
    </ul>
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
        amountOrdered: 0,
      }
    },
    methods: {
      incrementOrder: function() {
        this.amountOrdered += 1;
        this.$emit('orderedBurger', { name:   this.burger.name, 
                                amount: this.amountOrdered 
                              }
        );
      },
      decrementOrder: function() {
        if (this.amountOrdered > 0) {
          this.amountOrdered -= 1;
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.burgerImage {
  width: 200px;
  height: 200px;
}

h4 {
  text-align: center;
}

.ingredient {
    font-weight: bold;
    color: white;
}

.arrow {
  border: none;
  background-color: transparent;
  background-size: contain;
  background-repeat: no-repeat;
  background-position: center;
  width: 20px;
  height: 20px;
  position: relative;
  cursor: pointer;
}

#upArrow {
  background-image: url("/img/upArrow.png");
}

#downArrow {
  background-image: url("/img/downArrow.png");
}

</style>