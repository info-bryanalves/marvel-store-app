<template>
  <div>
    <Header v-bind:quantityCart="quantityCart"></Header>
    <Cart
    v-bind:quantityCart="quantityCart"
    v-bind:cart="cart"
    :characters="characters"
    v-bind="{ formatMoney, addToCart, decreaseItem, removeItem }"></Cart>
    <div class="container-fluid" style="background-color: #edecee;">
      <div class="row justify-content-center">
        <div v-show="showWaitMessage">
          <span class="wait-message">Aguarde...</span> <div class="spinner"></div>
        </div>
        <div
        class="col-5 col-md-3 col-lg-2 character-box"
        :key="character.id" v-for="character in characters">
          <div :id="'cart-message-' + character.id" class="alert alert-success cart-message"
          style="display:none" role="alert">
            Adicionado ao carrinho com sucesso!
          </div>
          <div style="text-align:center">
            <div class="character-thumbnail">
              <img :src="
              character.thumbnail.path +
              '/portrait_medium.' +
              character.thumbnail.extension"
              >
            </div>
            <div class="character-name">
              <a style="display:block" href="#">{{ character.name }}</a>
              <span>{{ formatMoney(character.price) }}</span>
            </div>
            <div>
              <a class="btn btn-outline-secondary"
              @click="addToCart(character.id);showCartMessage(character.id)">Adicionar</a>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import Header from '@/components/Header';
import Cart from '@/components/Cart';

export default {
  name: 'Index',
  components: {
    Header,
    Cart,
  },
  data() {
    return {
      characters: {},
      price: 0,
      cart: [],
      addItem: true,
      showWaitMessage: true,
      quantityCart: 0,
      name: 'Bryan Alves t',
      result2: {},
    };
  },
  created() {
    this.listCharacters();
  },
  methods: {
    listCharacters() {
      axios.get('http://localhost:8000/api/marvel')
        .then((response) => {
          this.showWaitMessage = false;
          this.characters = response.data;
        });
    },
    addToCart(characterIndex) {
      this.addItem = true;

      this.cart.forEach((element, index) => {
        if (element.id === this.characters[characterIndex].id) {
          this.addItem = false;
          this.cart[index].quantity += 1;
          this.quantityCart += 1;

          this.cart.push('');
          this.cart = this.cart.filter(v => v !== '');
        }
      });

      if (this.addItem) {
        this.characters[characterIndex].quantity = 1;
        this.quantityCart += 1;

        this.cart.push(this.characters[characterIndex]);
      }
    },
    decreaseItem(characterIndex) {
      this.cart.forEach((element, index) => {
        if (element.id === characterIndex) {
          this.cart[index].quantity -= 1;
          this.quantityCart -= 1;

          this.cart.push('');
          this.cart = this.cart.filter(v => v !== '');

          if (this.cart[index].quantity === 0) {
            this.cart.splice(index, 1);
          }
        }
      });
    },
    removeItem(characterIndex) {
      this.cart.forEach((element, index) => {
        if (element.id === characterIndex) {
          this.quantityCart -= element.quantity;
          this.cart.splice(index, 1);
        }
      });
    },
    showCartMessage(characterIndex) {
      document.getElementById(`cart-message-${characterIndex}`).style.display = 'block';

      setTimeout(() => {
        document.getElementById(`cart-message-${characterIndex}`).style.display = 'none';
      }, 3000);
    },
    formatMoney(price) {
      return price.toFixed(2).replace('.', ',').replace(/(\d)(?=(\d{3})+,)/g, '$1.');
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.character-box {
  background-color: #fff;
  width: 100%;
  padding: 8px;
  margin-top: 10px;
  margin-bottom: 20px;
  border-radius: 4px;
  min-height: 290px;
  margin-right:10px;
  padding-top:20px;
}

.character-thumbnail {
  margin-bottom: 20px;
}

.character-name {
  margin-bottom: 20px;
}

.wait-message {
  float:left;
}

@keyframes spin {
  to { transform: rotate(360deg);}
}
.spinner {
  border: 5px solid rgba(0, 0, 0, 0.1);
  border-left-color: #22a6b3;
  border-radius: 50%;
  width: 25px;
  height: 25px;
  -webkit-animation: spin-data-v-331f341c 1.2s linear infinite;
  animation: spin-data-v-331f341c 1.2s linear infinite;
  float:right;
}

.cart-message {

}
</style>
