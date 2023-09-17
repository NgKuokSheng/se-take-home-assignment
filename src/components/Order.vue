<template>
  <div>
    <span> ID: {{order.id}}</span>
    <span> Type: {{order.type}} </span>
  </div>
</template>

<script>
export default {
  name: "Order",
  props: {
    order: Object
  }
};
</script>