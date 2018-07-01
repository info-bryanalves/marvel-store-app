<template>
  <div>
    <Header></Header>
    <Cart v-bind:price="price"></Cart>
    <div class="container-fluid" style="background-color: #edecee;">
      <div class="row justify-content-center">
        <div
        class="col-5 col-md-3 col-lg-2 character-box"
        :key="character.id" v-for="character in characters">
          <div style="text-align:center">
            <div class="character-thumbnail">
              <img :src="
              character.thumbnail.path +
              '/portrait_medium.' +
              character.thumbnail.extension"
              >
            </div>
            <div class="character-name">
              <a style="display:block" href="#">{{ character.id}} - {{ character.name }}</a>
              <span>{{ formatMoney(character.price) }}</span>
            </div>
            <div>
              <a class="btn btn-outline-secondary" @click="alert(character.id)">Adicionar</a>
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
      cart: {},
    };
  },
  created() {
    this.listCharacters();
  },
  methods: {
    listCharacters() {
      axios.get('http://localhost:8000/api/marvel')
        .then((response) => {
          this.characters = response.data.results;
          console.log(this.characters);
        });
    },
    addToCart(characterId) {
      console.log(characters);
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
</style>
