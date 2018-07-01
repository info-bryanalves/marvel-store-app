<template>
  <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
    <img src="../assets/logo3.png" style="width:125px;margin-left:25px">
    <a href="#" data-toggle="modal" data-target="#cartModal">
      <img src="../assets/cart.png" class="cart-image">
      <span class="cart-quantity ">
        {{ quantityCart }}
      </span>
    </a>
  </nav>
</template>

<script>
export default {
  name: 'Header',
  props: {
    quantityCart: { type: Number },
  },
  data() {
    return {
    };
  },
};
</script>

<style scoped>
.cart-quantity {
  top: 10px;
  right: 45px;
  position: absolute;
  color: #343a40;
  background-color: white;
  border-radius: 30px;
  width: 25px;
  text-align: center;
  font-weight: bold;
}

.cart-image {
  position: absolute;
  right: 60px;
  top: 20px;
}
</style>
