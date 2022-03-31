<template>
  <div>
    <div v-if="$apollo.queries.products.loading">Loading...</div>
    <div v-else>
      <div class="grid-wrapper">
        <product
          v-for="(product, index) in products.items"
          :key="index"
          :name="product.name"
          :image="product.small_image.url"
          :price="product.price_range.minimum_price.regular_price"
          :sku="product.sku"
        ></product>
      </div>
      <b-pagination
        v-model="currentPage"
        :total-rows="products.total_count"
        :per-page="10"
      ></b-pagination>
    </div>
  </div>
</template>

<script>
import gql from 'graphql-tag'
import product from '@/components/ProductItem.vue'

const PRODUCTS = gql`
  query ($id: String!, $currentPage: Int!, $pageSize: Int!) {
    products(
      filter: { category_uid: { eq: $id } }
      currentPage: $currentPage
      pageSize: $pageSize
    ) {
      total_count
      items {
        name
        sku
        small_image {
          url
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
  name: 'ProductsView',
  components: {
    product
  },
  apollo: {
    products: {
      query: PRODUCTS,
      variables () {
        return {
          id: this.id,
          currentPage: this.currentPage,
          pageSize: this.pageSize
        }
      },
      update: (data) => data.products
    }
  },
  data () {
    return {
      currentPage: 1,
      products: [],
      pageSize: 10,
      id: this.$route.params.id
    }
  },
  watch: {
    '$route.params.id': function () {
      this.id = this.$route.params.id
      this.product = []
      this.currentPage = 1
    }
  },
  methods: {
    changePage (index) {
      this.products = []
      this.currentPage = index
    },
    previous () {
      if (this.currentPage > 1) {
        this.products = []
        this.currentPage--
      }
    },
    next () {
      if (this.currentPage < this.totalPages) {
        this.products = []
        this.currentPage++
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.grid-wrapper {
  padding: 2rem;
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  gap: 1rem;

  @media screen and (max-width: 1380px) {
    grid-template-columns: repeat(4, 1fr);
  }

  @media screen and (max-width: 991px) {
    grid-template-columns: repeat(3, 1fr);
  }

  @media screen and (max-width: 767px) {
    grid-template-columns: repeat(2, 1fr);
  }

  @media screen and (max-width: 540px) {
    grid-template-columns: 1fr;
  }
}

.pagination {
  list-style: none;
  display: flex;
  align-items: center;
  justify-content: center;
  li {
    font-size: 1rem;
    font-weight: 500;
    border: 1px solid #eee;
    margin: 0.0625rem;
    padding: 0.5rem;
    cursor: pointer;
    transition: 0.2s all ease-in-out;

    &.disabled {
      pointer-events: none; //This makes it not clickable
      opacity: 0.6; //This grays it out to look disabled
    }

    &:hover,
    &.active {
      background: #eee;
    }
  }
}
</style>
