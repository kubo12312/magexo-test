<template>
  <div
    class="item"
    @click="openProduct()"
  >
    <h3>{{ name }}</h3>
    <img :src="image">
    <p class="price">{{price.currency}} {{ price.value }}</p>
  </div>
</template>

<script>
export default {
  name: 'ProductItem',
  props: {
    name: String,
    image: String,
    price: Object,
    sku: String
  },
  data () {
    return {}
  },
  mounted () {},

  methods: {
    openProduct () {
      this.$router.push({
        name: 'ProductView',
        params: {
          sku: this.sku
        }
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.item {
  padding: 1rem;
  border: 1px solid #eee;
  background: #fff;
  transition: 0.2s all ease;
  cursor: pointer;
  display: flex;
  flex-direction: column;
  &:hover {
    transform: scale(1.1);
  }

  img {
    max-width: 100%;
    margin-top: auto;
  }

  .price {
    font-size: 1rem;
    font-weight: 700;
    margin-top: 1rem;
  }
}
</style>
