<template>
  <div class="modal fade" id="cartModal" tabindex="-1" role="dialog"
  aria-labelledby="cartModalLabel" aria-hidden="true">
    <div class="modal-dialog modal-lg" role="document">
      <div class="modal-content">
        <div class="modal-header cart-header">
          <h5 class="modal-title" id="cartModalLabel">Meu carrinho</h5>
          <div class="cart-subtotal-box">
            <div class="cart-subtotal-price">
              Total: R$ {{ price }}
            </div>
          </div>
          <button type="button" class="close cart-close" data-dismiss="modal" aria-label="Close">
            <span aria-hidden="true">&times;</span>
          </button>
        </div>
        <div class="modal-body">
          <table class="table">
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
              <tr>
                <th scope="row">1</th>
                <td>Mark</td>
                <td>
                  <input type="number" class="form-control" style="width:60px;" min="0" max="9">
                </td>
                <td>Otto</td>
                <td>@mdo</td>
                <td>
                  <button type="button" class="close cart-item-delete" aria-label="Close">
                    <span aria-hidden="true">&times;</span>
                  </button>
                </td>
              </tr>
              <tr>
                <th scope="row">2</th>
                <td>Jacob</td>
                <td>
                  <input type="number" class="form-control" style="width:60px;" min="0" max="9">
                </td>
                <td>Thornton</td>
                <td>@fat</td>
                <td>
                  <button type="button" class="close cart-item-delete" aria-label="Close">
                    <span aria-hidden="true">&times;</span>
                  </button>
                </td>
              </tr>
              <tr>
                <th scope="row">3</th>
                <td>Larry</td>
                <td>
                  <input type="number" class="form-control" style="width:60px;" min="0" max="9">
                </td>
                <td>the Bird</td>
                <td>@twitter</td>
                <td>
                  <button type="button" class="close cart-item-delete" aria-label="Close">
                    <span aria-hidden="true">&times;</span>
                  </button>
                </td>
              </tr>
            </tbody>
          </table>
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
    price: { type: Number },
  },
  data() {
    return {
      characters: {},
    };
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
