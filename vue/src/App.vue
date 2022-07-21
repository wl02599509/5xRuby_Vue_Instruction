<script setup>
import { createApp, ref, computed } from 'vue';

const list = ref([]);

const cart = ref([]);

const addToCart = newItem => {
  const idx = cart.value.findIndex(item => item.name === newItem.name);
  
  if (idx !== -1) {
    cart.value[idx].amount++;
  } else {
    cart.value.push({
      ... newItem,
      amount: 1
    })
  }
}

const removeFromCart = oldItem => {
  const idx = cart.value.findIndex(item => item.name === oldItem.name);
  cart.value.splice(idx, 1);
}

const sortedCart = computed(() => {
  
  const order = cart.value.sort((a, b) => b.price - a.price);
  
  const copyCart = [...cart.value];

  const orderPrice = [];
  
  copyCart.forEach((elm) => {
    for(let i = 0; i < elm.amount; i++){
      orderPrice.push(elm.price);
    }
  })

  return orderPrice
})

const sum = computed( () => {
  let result = 0;

  if (sortedCart.value.length >= 3){
    sortedCart.value.forEach((d, idx) => {
      result += (idx < 3) ? d * 0.8 : d;
    });
  } else {
    cart.value.forEach((elm) => {
      result += elm.price * elm.amount;
    })
  }

  return result;
}) 

const removeAll = () => {
  cart.value.splice(0, cart.value.length)
}





fetch('/list.json')
  .then(d => d.json())
  .then(res => {
    list.value = res;
  });

</script>

<template>
  <main>
    <header>
      <nav class="bg-light navbar navbar-expand-lg navbar-light">
        <div class="container-fluid">
          <a class="navbar-brand" href="/"><i class="fas fa-gem"></i> 賺很大商店</a>
          <div class="collapse navbar-collapse" id="navbarNav">
            <ul class="m-0 navbar-nav">
              <li class="nav-item inline">
                <a class="p-4 no-underline inline-block nav-link active hover:(bg-gray-300)" aria-current="page" href="#">商品</a>
              </li>
              <li class="nav-item inline">
                <a class="p-4 no-underline inline-block nav-link hover:(bg-gray-300)" href="#">當日特價</a>
              </li>
            </ul>
          </div>
        </div>
      </nav>
    </header>

    <section class="container mt-4">
      <div class="row items">
        <div 
        v-for="item in list" :key="item" 
        class="col-sm-2">
          <div class="card" data-product-id="624946E">
            <img :src="item.cover" class="card-img-top" alt="">
            <div class="card-body">
              <h5 class="card-title fw-light fs-6">{{ item.name }}</h5>
              <p class="price">{{ item.price }}</p>
              <button 
              @click="addToCart(item)"
              class="btn btn-sm btn-warning fw-light"><i class="fas fa-cat"></i></button>
            </div>
          </div>
        </div>
      </div>
      <hr>

      <section class="cart">
        <h2>購物車</h2>
        <table class="table cart-item-table">
          <thead>
            <tr>
              <th scope="col">項目</th>
              <th scope="col">數量</th>
              <th scope="col">單價</th>
              <th scope="col">小計</th>
              <th scope="col"></th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="item in cart" :key="item" >
              <td> {{ item.name }} </td>
              <td><input type="number" class="quantity" v-model="item.amount" /></td>
              <td class="price"> {{ item.price }} </td>
              <td class="sum"> {{ item.price * item.amount }} </td>
              <td>
                <button 
                @click="removeFromCart(item)"
                class="remove-item-btn btn btn-danger btn-sm">
                  <i class="fas fa-trash-alt"></i>
                </button>
              </td>
            </tr>
          </tbody>
          <tfoot>
            <tr>
              <td colspan="2"></td>
              <td>總價</td>
              <td><span class="total-price">$ {{ sum }}</span></td>
              <td></td>
            </tr>
          </tfoot>
        </table>
        <button 
        @click="removeAll()"
        class="btn btn-lg btn-success empty-cart"><i class="fas fa-baby-carriage"></i> 清空購物車</button>
      </section>
    </section>
  </main>
</template>
