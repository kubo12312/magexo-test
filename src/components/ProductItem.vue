<template>
  <b-card
    :title="name"
    :img-src="image"
    img-alt="Image"
    img-top
    class="mb-2"
  >
    <b-card-text>
      Price: <strong>{{price.value}} {{price.currency}}</strong>
    </b-card-text>

    <b-button
      :to="{name: 'ProductView',
        params: {
          sku: this.sku,
          name: this.name
        }}"
      variant="primary"
    >Detail</b-button>
  </b-card>
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
  mounted () {}
}
</script>

<style lang="scss" scoped>
.card-body {
  text-align: left;
}
</style>
