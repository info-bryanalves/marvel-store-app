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
          <button type="button" class="close cart-close" data-dismiss="modal" @click="boxPaymentCard=false" aria-label="Close">
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
          <div v-show="boxPaymentCard">
            <hr>
            <fieldset>
              <div class="row">
                <input type="text" class="StripeElement" placeholder="Nome" style="border:0">
              </div>
            </fieldset>
            <fieldset>
              <div class="row">
                <div ref="card"></div>
              </div>
            </fieldset>
            <div style="width:100%;text-align:right;margin-right:13px">
              <button class="btn btn-success" v-on:click="purchase">Confirmar</button>
            </div>
          </div>
        </div>
        <div class="modal-footer">
          <button class="btn btn-light" @click="boxPaymentCard=false" data-dismiss="modal">Fechar</button>
          <button class="btn btn-dark" @click="boxPaymentCard=true">Finalizar compra</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
const stripe = Stripe('pk_test_91y7NO3Bssfty3BpUlzGGc5I');
const elements = stripe.elements();
let card = '';

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
      boxPaymentCard: false,
      price: 0,
    };
  },
  computed: {
    computedCalc() {
      this.calc();
      return this.price;
    },
  },
  mounted() {
    const style = {
      iconStyle: 'solid',
      style: {
        base: {
          iconColor: '#c4f0ff',
          color: '#000',
          fontWeight: 500,
          fontFamily: 'Roboto, Open Sans, Segoe UI, sans-serif',
          fontSize: '16px',
        },
        invalid: {
          iconColor: '#FFC7EE',
          color: '#FFC7EE',
        },
      },
    };

    card = elements.create('card', style);
    card.mount(this.$refs.card);
  },
  methods: {
    calc() {
      this.price = 0;
      this.cart.forEach((element) => {
        this.price += element.price * element.quantity;
      });
    },
    purchase() {
      const self = this;
      stripe.createToken(card, { name: self.name }).then((result) => {

        console.log(result);
      });
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
fieldset {
  margin: 0 15px 20px;
  padding: 0;
  border-style: none;
  box-shadow: 0 6px 9px rgba(50, 50, 93, 0.06), 0 2px 5px rgba(0, 0, 0, 0.08);
  border-radius: 4px;
}

.row {
  align-items: center;
  margin-left: 15px;
  display:block;
}

.StripeElement {
  width: 100%;
  padding: 11px 15px 11px 0;
}

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
