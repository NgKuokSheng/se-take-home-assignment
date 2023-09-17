<template>
  <div>
    <button @click="addOrder">New Normal Order</button>
    <button @click="addVipOrder">New VIP Order</button>
    <button @click="addBot">+ Bot</button>
    <button @click="removeBot">- Bot</button>

    <div>
      <h2>Pending Orders</h2>
      <Order v-for="order in pendingOrder" :key="order.id" :order="order" />
    </div>

    <div>
      <h2>Completed Orders</h2>
      <Order v-for="order in completedOrder" :key="order.id" :order="order" />    </div>
    </div>

    <div>
      <h2>Bots</h2>
      <div v-for="bot in bots" :key="bot.id">  
        <span>Bot ID: {{bot.id}}</span>
        <span v-if="bot.currentOrder"> Processing Order {{bot.currentOrder.id}} </span>
        <span v-else>Status: IDLE</span>
      </div>
    </div>
</template>

<script>
const delay = ms => new Promise(res => setTimeout(res,ms));
const PENDING = "pending", PROCESSING = "processing", COMPLETED = "completed"

import Order from "./components/Order.vue";
export default {
  components: {
    Order
  },
  data() {
    return {
      orders: [],
      completed: [],
      bots: [],
      orderNumber: 1,
    };
  },
  computed: {
    pendingOrder(){
      return this.orders.filter(order => order.status === PENDING);
    },
    completedOrder(){
      return this.orders.filter(order => order.status === COMPLETED);
    },
  },
  methods: {
    customOrderSort(orderA, orderB) {
      if (orderA.type === "VIP" && orderB.type !== "VIP") {
        return -1; // Order A is VIP, so it comes before Order B
      } else if (orderA.type !== "VIP" && orderB.type === "VIP") {
        return 1; // Order B is VIP, so it comes before Order A
      } else {
        return orderA.id - orderB.id; // Sort by order number if both orders are of the same type
      }
    },
    addOrder() {
      let order = { status : PENDING, type : "normal" }
      order.id = this.orderNumber++
      this.orders.push(order)
      this.orders.sort(this.customOrderSort)
    },
    addVipOrder() {
      let order = { status : PENDING, type : "VIP" }
      order.id = this.orderNumber++
      this.orders.push(order)
      this.orders.sort(this.customOrderSort)
    },
    addBot() {
      let bot = {}
      bot.id = this.bots.length + 1
      this.bots.push(bot)
      this.processOrder(bot)
    },
    removeBot() {
      this.bots.pop();
    },
    getNextOrder(){
      let order = this.orders.find(order => order.status === PENDING);
      if(order)
        this.updateOrderStatus(order.id, PROCESSING)
      return order
    },
    async processOrder(bot){
      while (true) {
        bot.currentOrder = this.getNextOrder();

        if (bot.currentOrder) {

          // Simulate order processing time of 10s
          await delay(10000)

          // Update the order status to "COMPLETE"
          this.updateOrderStatus(bot.currentOrder.id, COMPLETED)
        } else {
          // No more pending orders, the bot remains idle
          await delay(2000); // polling for new pending order
        }
      }
    },
    updateOrderStatus(orderId, newStatus) {
      const orderToUpdate = this.orders.find(order => order.id === orderId);
      if (orderToUpdate) 
        orderToUpdate.status = newStatus;
    }
  }
};
</script>