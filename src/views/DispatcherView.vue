<template>
    <div id="orders">
      <div id="orderList">
        <div v-for="(order, key) in orderList" v-bind:key="'order'+key">
          #{{ order.orderId}}: {{ formatOrderItems(order.orderItems, order.customerInfo) }}
        </div>
        <button v-on:click="clearQueue">Clear Queue</button>
      </div>

     <div id="dots">
     <div v-for="(order, index) in orderList"
      :key ="'dot' + order.orderId"
      :style = "{ left: order.details.x + 'px', top: order.details.y + 'px' }"
      >
      T
      </div>
      </div>
    </div>
  </template>

  <script>
  import io from 'socket.io-client'
  const socket = io("localhost:3000"); // Koppla upp socket mot servern
  
  export default {
    name: 'DispatcherView',
    data: function () {
      return {
        orders: {}
      }
    },

    computed: {
      orderList(){
        return Object.values(this.orders || {}) // gör om objekt tilll array
      }
      },

    created: function () {     // lyssnar på uppdateringar från servern
      socket.on('currentQueue', (data) => {
        console.log('currentQueue data received:', data);
        this.orders = data.orders || {};
        });
      },

    methods: {
      clearQueue: function () {
        socket.emit('clearQueue');
      },
      changeStatus: function(orderId) {
        socket.emit('changeStatus', {orderId, status: "Annan status"});
      },

    formatOrderItems(orderItems, costumerInfo) {
      if (!orderItems) return "";
      // orderItems är objekt: { "Cheeseburger": 2, ... }
      if (!costumerInfo) return "";

      const orderText = Object.entries(orderItems)
        .map(([name, qty]) => `${name} (${qty})`)
        .join(", ");
      
      const costumerText = 
        `${costumerInfo.name}, `+
        `${costumerInfo.paymentmethod}, `+
        `${costumerInfo.gender}, `+
        `${costumerInfo.email}`;

      return `${orderText} - ${costumerText}`;
       
  },
  },
}
</script>

  <style>
  #orderList {
    top:1em;
    left:1em;
    position: absolute;
    z-index: 2;
    color:black;
    background: rgba(255,255,255, 0.5);
    padding: 1em;
  }
  #dots {
    position: relative;
    margin: 0;
    padding: 0;
    background-repeat: no-repeat;
    width:1920px;
    height: 1078px;
    cursor: crosshair;
    background-image: url('/img/polacks.jpg');
  }
  
  #dots div {
    position: absolute;
    background: black;
    color: white;
    border-radius: 10px;
    width:20px;
    height:20px;
    text-align: center;
  }


#target {
  position: absolute;
  width: 20px;
  height: 20px;
  color: white;
  background: red;
  border-radius: 50%;
  text-align: center;
  line-height: 20px;
  font-weight: bold;
}
  </style>
  