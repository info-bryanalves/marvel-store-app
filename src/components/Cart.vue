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
          <button type="button" class="close cart-close" data-dismiss="modal"
          @click="boxPaymentCard=false" aria-label="Close">
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
            <div :class="'alert alert-'+typeMessage" role="alert" v-if="showPaymentMessage">
                {{ paymentMessage }}
            </div>
            <fieldset>
              <div class="row">
                <input type="text" class="StripeElement"
                placeholder="Nome" style="border:0" v-model="cardUserName">
              </div>
            </fieldset>
            <fieldset>
              <div class="row">
                <input type="email" class="StripeElement"
                placeholder="Email" style="border:0" v-model="cardUserEmail">
              </div>
            </fieldset>
            <fieldset>
              <div class="row">
                <div ref="card"></div>
              </div>
            </fieldset>
            <div style="width:100%;text-align:right;margin-right:13px">
              <button class="btn btn-success" v-on:click="getToken(sendToken);" id="btn-confirmar" style="float:right">Confirmar</button>
              <div style="margin-top: 5px;float: right;margin-right: 15px;" v-show="showWaitMessage">
                <span class="wait-message">Aguarde...</span><div class="spinner"></div>
              </div>
            </div>
          </div>
        </div>
        <div class="modal-footer">
          <button class="btn btn-light" @click="boxPaymentCard=false"
          data-dismiss="modal">Fechar</button>
          <button class="btn btn-dark" @click="showBoxPaymentCard">Finalizar compra</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

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
      cardUserName: '',
      cardUserEmail: '',
      token: {},
      price: 0,
      typeMessage: 'success',
      paymentMessage: [],
      showPaymentMessage: false,
      showWaitMessage: false,
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
    validateFields() {
      if (this.cardUserName.trim() === '') {
        this.typeMessage = 'danger';
        this.showPaymentMessage = true;
        this.paymentMessage = 'Informe seu nome';

        return false;
      } else if (this.cardUserEmail.trim() === '') {
        this.typeMessage = 'danger';
        this.showPaymentMessage = true;
        this.paymentMessage = 'Informe seu email';

        return false;
      }

      return true;
    },
    getToken(callback) {
      if (this.validateFields()) {
        this.showWaitMessage = true;
        document.getElementById('btn-confirmar').disabled = true;
        const self = this;
        stripe.createToken(card, { name: self.cardUserName }).then((result) => {
          if (result.error) {
            self.typeMessage = 'danger';
            self.paymentMessage = self.translateError(result.error.code);
            self.showPaymentMessage = true;
            self.showWaitMessage = false;
            document.getElementById('btn-confirmar').disabled = false;
          } else {
            self.showPaymentMessage = false;

            callback.call(self, result.token);
          }
        });
      }
    },
    sendToken(token) {
      axios.post('http://localhost:8000/api/payment/register', {
        amount: this.price,
        receipt_email: this.cardUserEmail,
        token: token.id,
      })
        .then((response) => {
          this.showWaitMessage = false;
          document.getElementById('btn-confirmar').disabled = false;
          this.showPaymentMessage = true;
          this.typeMessage = 'success'
          this.paymentMessage = 'Pagamento realizado com sucesso!';
        })
        .catch((error) => {
          this.showWaitMessage = false;
          document.getElementById('btn-confirmar').disabled = false;
          this.showPaymentMessage = true;
          this.typeMessage = 'danger';
          this.paymentMessage = 'Erro interno do servidor';
        });
    },
    showBoxPaymentCard() {
      if (this.quantityCart > 0) {
        this.boxPaymentCard = true;
      }
    },
    translateError(message) {
      switch(message) {
        case 'incomplete_number':
          return 'Número de cartão incompleto';
        case 'invalid_number':
          return 'Número de cartão inválido';
        case 'incomplete_expiry':
          return 'Data de expiração incompleta';
        case 'invalid_expiry_year':
          return 'Ano de expiração inválido';
        case 'invalid_expiry_year_past':
          return 'Ano de experição passado, verifique se o cartão está vencido';
        case 'incomplete_cvc':
          return 'Codigo de seguranção incompleto';
      }
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

.wait-message {
  float:left;
}

@keyframes spin {
  to { transform: rotate(360deg);}
}
.spinner {
  float:left;
  border: 5px solid rgba(0, 0, 0, 0.1);
  border-left-color: #22a6b3;
  border-radius: 50%;
  width: 25px;
  height: 25px;
  -webkit-animation: spin-data-v-331f341c 1.2s linear infinite;
  animation: spin-data-v-331f341c 1.2s linear infinite;
  float:right;
}
</style>
