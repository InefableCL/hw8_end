<template>
  <div :class="{ headerBasketActive: showBasket }" class="header-basket">
    <div class="header-basket-product">
      <div v-for="item in cartItems" :key="item.id_product" class="basket-product">
        <div class="basket-image">
          <img :src="imgCartProduct" :alt="item.product_name" />
        </div>
        <p class="basket-product-title">
          {{ item.product_name }}
        </p>
        <p class="basket-product-price">
          {{ item.price }}
        </p>
        <button @click="removeProductCart(item)" class="basket-delete-product">-</button>
        <p class="basket-product-quantity">
          {{ item.quantity }}
        </p>
        <button @click="addProductCart(item)" class="basket-delete-product">+</button>
      </div>
    </div>
    <p v-if="cartItems.length == 0">Корзина пуста</p>
    <div v-else class="basket-total">Итого: {{ total.sum }}<span></span></div>
  </div>
</template>
<script>
import { API_FOR_CART } from 'js/constants'
import http from "js/server"
export default {
  data: function() {
    return {
      imgCartProduct: 'http://placehold.it/50x50',
      cartItems: [],
    }
  },
  props: {
    showBasket: {
      type: Boolean,
      default: false,
    },
  },
  created() {
    http
      .get(API_FOR_CART.goodsFromCart)
      .then((data) => {
        for (let el of data) {
          let prod = { ...el, imgProduct: 'http://placehold.it/350x300' }
          this.cartItems.push(prod)
        }
      })
      .catch((e) => console.log(e))
  },

  methods: {
    addProductCart(product) {
      let findElem = this.cartItems.find((elem) => elem.id_product === product.id_product)
      if (findElem) {
        http
          .put(API_FOR_CART.incToCart, { id: product.id_product })
          .then(({ result }) => {
            if (+result === 1) {
              findElem.quantity++
            } else {
              throw result
            }
          })
          .catch((e) => console.error(e))
      } else {
        http
          .post(API_FOR_CART.addToCart, { product })
          .then(({ result }) => {
            if (+result === 1) {
              let cartGood = { ...product }
              this.cartItems.push(cartGood)
            } else {
              throw result
            }
          })
          .catch((e) => console.error(e))
      }
    },
    removeProductCart(product) {
      let findElem = this.cartItems.find((elem) => elem.id_product === product.id_product)
      if (findElem.quantity > 1) {
        http
          .put(API_FOR_CART.decToCart, { id: product.id_product })
          .then(({ result }) => {
            if (+result === 1) {
              findElem.quantity--
            } else {
              throw result
            }
          })
          .catch((e) => console.error(e))
      } else {
        http
          .delete(API_FOR_CART.removeFromCart + `/${product.id_product}`)
          .then(({ result }) => {
            if (+result === 1) {
              let idx = this.cartItems.findIndex((elem) => elem.id_product === product.id_product)
              this.cartItems.splice(idx, 1)
            } else {
              throw result
            }
          })
          .catch((e) => console.error(e))
      }
    },
    sendResultToParentComponent() {
      this.$emit('sentresult', {
        allSum: this.total.sum,
        allQuantity: this.total.quantity,
      })
    },
  },
  watch: {
    total() {
      this.sendResultToParentComponent()
    },
  },
  computed: {
    total() {
      return {
        sum: this.cartItems.reduce((val, elem) => val + elem.quantity * elem.price, 0),
        quantity: this.cartItems.reduce((val, elem) => val + elem.quantity, 0),
      }
    },
  },
}
</script>
