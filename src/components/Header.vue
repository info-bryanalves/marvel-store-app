<template>
  <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
    <img src="../assets/logo3.png" style="width:125px;margin-left:25px">

    <button class="navbar-toggler" type="button" data-toggle="collapse"
    data-target="#navbarSupportedContent" aria-controls="navbarSupportedContent"
    aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
    </button>

    <div class="collapse navbar-collapse" id="navbarSupportedContent">
      <ul class="navbar-nav mr-auto">
        <li class="nav-item active">
            <a class="nav-link" href="#">Home <span class="sr-only">(current)</span></a>
        </li>
      </ul>
      <div style="margin-right:50px">
        <a href="#" data-toggle="modal" data-target="#cartModal">
          <img src="../assets/cart.png">
        </a>
      </div>
    </div>
  </nav>
</template>

<script>
export default {
  name: 'Header',
  data() {
    return {
    };
  },
};
</script>
