<template>
  <div class="modal fade" id="cartModal" tabindex="-1" role="dialog"
  aria-labelledby="cartModalLabel" aria-hidden="true">
    <div class="modal-dialog modal-lg" role="document">
      <div class="modal-content">
        <div class="modal-header cart-header">
          <h5 class="modal-title" id="cartModalLabel">Meu carrinho</h5>
          <div class="cart-subtotal-box">
            <div class="cart-subtotal-price">
              Total: R$ {{ formatMoney(computedCalc) }}
            </div>
          </div>
          <button type="button" class="close cart-close" data-dismiss="modal" aria-label="Close">
            <span aria-hidden="true">&times;</span>
          </button>
        </div>
        <div class="modal-body">
          <table class="table" v-if="cart.length > 0">
            <thead>
              <tr>
                <th scope="col">Foto</th>
                <th scope="col">Personagem</th>
                <th scope="col">Quantidade</th>
                <th scope="col">Val. Unitário</th>
                <th scope="col">SubTotal</th>
                <th scope="col">&nbsp;</th>
              </tr>
            </thead>
            <tbody>
              <tr :key="item.id" v-for="item in cart">
                <th scope="row">
                  <img :src="
                  item.thumbnail.path +
                  '/portrait_small.' +
                  item.thumbnail.extension"
                  >
                </th>
                <td>{{ item.name }}</td>
                <td>
                  <img src="../assets/less.png" style="cursor:pointer;"
                  @click="decreaseItem(item.id)">
                  {{ item.quantity }}
                  <img src="../assets/more.png" style="cursor:pointer;"
                  @click="addToCart(item.id)">
                </td>
                <td>{{ formatMoney(item.price) }}</td>
                <td>{{ formatMoney(item.quantity * item.price) }}</td>
                <td>
                  <a href="#" class="close cart-item-delete"
                  aria-label="Close" @click="removeItem(item.id)">
                    <span aria-hidden="true">&times;</span>
                  </a>
                </td>
              </tr>
            </tbody>
          </table>
          <div v-else style="text-align:center">
            <img src="../assets/deadpool-triste.png">
            <span style="font-weight: bold;">Carrinho vazio</span>
          </div>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
          <button type="button" class="btn btn-primary">Save changes</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Cart',
  props: {
    cart: { type: Array },
    formatMoney: { type: Function },
    addToCart: { type: Function },
    decreaseItem: { type: Function },
    removeItem: { type: Function },
    quantityCart: { type: Number },
    characters: { type: Object },
  },
  data() {
    return {
      price: 0,
    };
  },
  computed: {
    computedCalc() {
      this.calc();
      return this.price;
    },
  },
  methods: {
    aqui() {
      console.log(this.cart);
    },
    calc() {
      this.price = 0;
      this.cart.forEach((element) => {
        this.price += element.price * element.quantity;
      });
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.cart-header {
  background-color: #262626;
}

.cart-header h5 {
  color:white;
}

.cart-close {
  color:white;
}

.cart-item-delete {
  color:red;
}

.cart-subtotal-box {
  width: 75%;
  text-align: center;
}

.cart-subtotal-price {
  background-color:#0d0d0d;
  color:white;
  border-radius: 5px;
  width: 30%;
  margin: 0 auto;
}

.modal-body {
  padding: 0 1rem 1rem 1rem;
}
</style>
