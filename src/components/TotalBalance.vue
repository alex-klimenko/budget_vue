<template>
  <div
    class="total-value"
    :class="{'total-value-positive': total > 0,'total-value-negative': total < 0}"
  >Balance: {{ total }}</div>
</template>

<script>
export default {
  name: "TotalBalance",
  props: {
    total: {
      type: Number,
      default: 0
    }
  }
};
</script>

<style scoped>
.total-value {
  font-size: 26px;
  text-transform: uppercase;
  padding: 20px;
  text-align: center;
}
.total-value-positive {
  color: green;
}
.total-value-negative {
  color: red;
}
</style>