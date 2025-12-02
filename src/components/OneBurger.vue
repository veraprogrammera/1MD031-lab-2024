<template>   
    
    <div class="box">
        <h2>{{burger.name}}</h2>
        <img :src="'/img/'+ burger.url" :alt="burger.name">

                    <p> Cheese level:{{ burger.cheese}} </p>
                        <ul>
                            <li v-if="burger.gluten">Containes <span class="allergi">gluten</span></li>
                            <li v-if="burger.lactose">Containes <span class="allergi">lactose</span></li>
                            <li>Containes{{burger.cheese}} </li>
                        </ul>

                        <p>Amount ordered: {{ amountOrdered }}</p>
                        <p>
                          <button @click="decreaseAmount">-</button>
                          <button @click="increaseAmount">+</button>
                        </p>
                </div>
</template>   

<style scoped>
.burgers {
     display: grid;
     grid-gap: 2.5%;
     grid-template-columns: 32% 32% 32%;
     justify-content: center;
 }

 .box{
    border-radius: 5px;
    padding: 20px;
    margin-top: 30px;
    margin-bottom: 30px;
    border: 3px solid rgb(122, 63, 27);
 }

 .box h2 {
  min-height: 2.4em;   /* så att alla har två rader text */
  line-height: 1.2;
  text-align: center;
  display: flex;
  align-items: center;      /* vertikalt centrera */
  justify-content: center;  /* horisontellt center */
}

img {
    width: 200px;
    height: 220px;
    object-fit: cover;
    display: block;
    margin: 0 auto;
}
</style>

<script>
export default {
  name: 'OneBurger',
  props: {
    burger: Object
  },

 data: function () {
    return {
      amountOrdered: 0   // hur många av denna specifika burgare kunden valt
    }
  },

  methods: {
    increaseAmount: function () {
      this.amountOrdered = this.amountOrdered + 1;
      this.$emit('orderedBurger', {
      name: this.burger.name,
      amount: this.amountOrdered
    });
    },

    decreaseAmount: function () {
      if (this.amountOrdered > 0) {
        this.amountOrdered = this.amountOrdered -1;
        
        this.$emit('orderedBurger', {
        name: this.burger.name,
        amount: this.amountOrdered
      });
    }
  }
}
}
</script>