<template>
  <div class="product-block">
    <div class="product-block-img">
      <img :src="item.imgProduct" :alt="item.product_name" />
    </div>
    <div class="product-block-content">
      <h2 class="product-block-title">{{ item.product_name }}</h2>
      <p class="product-block-desc">Lorem ipsum dolor sit amet consectetur adipisicing elit. Similique, at.</p>
      <div class="product-block-quantity">
        <p class="product__price">{{ item.price }} руб.</p>
        <div @click="addProductCart(item)" class="product-add-basket">
          
          <span> Add to </span>
          
          <img src="./../assets/shopping-basket.svg" alt="В корзину" />
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  data: function () {
    return {}
  },
  props: {
    item: {
      type: Object,
      required: true,
    },
  },
  methods: {
    addProductCart(item) {
      this.$root.$refs.header.$refs.cart.addProductCart(item)
    },
  },
}
</script>
