<template>
  <div v-if="$apollo.queries.productGallery.loading">Loading...</div>
  <div
    v-else
    class="grid"
  >
    <div>
      <lingallery
        :iid.sync="currentId"
        :width="width"
        :height="height"
        :items="images"
      />
    </div>
    <div class="text-left">
      <h1>{{ product.name }}</h1>
      <span
        class="description"
        v-html="product.description.html"
      ></span>
      <p class="price"><span>Price:</span> {{ product.price_range.minimum_price.regular_price.value }} {{ product.price_range.minimum_price.regular_price.currency }}</p>
    </div>
  </div>
</template>

<script>
import gql from 'graphql-tag'
import Lingallery from 'lingallery'

const PRODUCT_GALLERY = gql`
  query ($sku: String!) {
    productDetail: products(filter: { sku: { eq: $sku } }) {
      total_count
      items {
        media_gallery {
          url
          label
        }
      }
    }
  }
`

const PRODUCT = gql`
  query ($sku: String!) {
    products(filter: { sku: { eq: $sku } }) {
      items {
        name
        description {
          html
        }
        price_range {
          minimum_price {
            regular_price {
              value
              currency
            }
          }
        }
      }
    }
  }
`

export default {
  name: 'ProductView',
  props: {
    sku: String
  },
  components: {
    Lingallery
  },
  apollo: {
    productGallery: {
      query: PRODUCT_GALLERY,
      variables () {
        return {
          sku: this.sku
        }
      },
      update: (data) => data.productDetail.items[0]
    },
    product: {
      query: PRODUCT,
      variables () {
        return {
          sku: this.sku
        }
      },
      update: (data) => data.products.items[0]
    }
  },

  data () {
    return {
      productGallery: [],
      product: [],
      currentId: null,
      width: 600,
      height: 400
    }
  },

  computed: {
    images () {
      const images = []
      for (let i = 0; i < this.productGallery.media_gallery.length; i++) {
        images.push({
          src: this.productGallery.media_gallery[i].url,
          thumbnail: this.productGallery.media_gallery[i].url
        })
      }
      console.log(images)
      return images
    }
  },

  methods: {}
}
</script>

<style lang="scss" scoped>
.grid {
  max-width: max(70%, 960px);
  margin: 3rem auto 0;
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1rem;

  .text-left {
    text-align: left;
  }

  .price {
    font-size: 1.5rem;
    font-weight: 700;
    span {
      opacity: 0.6;
      font-weight: 400;
    }
  }
}
</style>
