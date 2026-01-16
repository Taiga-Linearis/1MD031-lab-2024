<template>
    <div id="orders">
      <div id="orderList">
        <div v-for="(order, key) in orders" v-bind:key="'order'+key">
          <div>
            <strong>#{{ key }}</strong>
          </div>

          <div>
            <strong>Items:</strong>
            <span v-if="Array.isArray(order.orderItems)">
              {{ order.orderItems.join(", ") }}
            </span>
            <span v-else>
              <template v-for="(amount, name) in order.orderItems" :key="name">
                {{ name }} — {{ amount }}
              </template>
            </span>
          </div>

          <div v-if="order.customer">
            <strong>Customer:</strong>
            <div>{{ order.customer.fullName }}</div>
            <div>{{ order.customer.email }}</div>
            <div>{{ order.customer.payment }} — {{ order.customer.gender }}</div>
          </div>

          <div v-if="order.details">
            <strong>Location:</strong> x: {{ order.details.x }}, y: {{ order.details.y }}
          </div>
        </div>
        <button v-on:click="clearQueue">Clear Queue</button>
      </div>
      <div id="dots">
          <div v-for="(order, key) in orders"
               v-bind:style="{ left: (order.details && order.details.x ? order.details.x : 0) + 'px', top: (order.details && order.details.y ? order.details.y : 0) + 'px'}"
               v-bind:key="'dots' + key">
            {{ key }}
          </div>
      </div>
    </div>
  </template>
  <script>
  import io from 'socket.io-client'
  const socket = io("http://localhost:3000");
  
  export default {
    name: 'DispatcherView',
    data: function () {
      return {
        orders: {},
      }
    },
    created: function () {
      socket.on('currentQueue', data => {
        this.orders = data && data.orders ? data.orders : {};
      });
    },
    methods: {
      clearQueue: function () {
        socket.emit('clearQueue');
      },
      changeStatus: function(orderId) {
        socket.emit('changeStatus', {orderId: orderId, status: "Annan status"});

      },
    }
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
  </style>
