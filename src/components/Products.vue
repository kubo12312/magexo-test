<template>
  <div>
    <div v-if="$apollo.queries.products.loading">
      <b-spinner variant="primary"></b-spinner>
    </div>
    <div v-else>
      <div class="wrapper">
        <h1 v-if="createTitle">{{ createTitle}}</h1>
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
        <ul
          class="pagination"
          v-if="products.total_count > pageSize"
        >
          <li
            @click="previous()"
            :class="{ disabled: currentPage === 1 }"
          >
            <font-awesome-icon icon="fa-solid fa-angle-left" />
          </li>
          <li
            v-for="index in totalPages"
            :key="index"
            :class="{ active: index === currentPage }"
            @click="changePage(index)"
          >
            {{index}}
          </li>
          <li
            @click="next()"
            :class="{ disabled: currentPage === totalPages }"
          >
            <font-awesome-icon icon="fa-solid fa-angle-right" />
          </li>
        </ul>
      </div>
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
  props: {
    id: String,
    title: {
      type: String,
      default: 'Home'
    }
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
      pageSize: 12
    }
  },
  watch: {
    id (newId, oldId) {
      this.currentPage = 1
      this.$apollo.queries.products.refetch({
        id: newId,
        currentPage: 1,
        pageSize: this.pageSize
      })
    }
  },
  computed: {
    createTitle () {
      return this.title.replaceAll('-', ' ')
    },
    totalPages () {
      return Math.ceil(this.products.total_count / this.pageSize)
    }
  },
  methods: {
    changePage (index) {
      this.products = []
      this.currentPage = index
      this.refetch()
    },
    previous () {
      if (this.currentPage > 1) {
        this.products = []
        this.currentPage--
        this.refetch()
      }
    },
    next () {
      if (this.currentPage < this.totalPages) {
        this.products = []
        this.currentPage++
        this.refetch()
      }
    },
    refetch () {
      this.$apollo.queries.products.refetch({
        id: this.id,
        currentPage: this.currentPage,
        pageSize: this.pageSize
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.wrapper {
  max-width: 1200px;
  width: 100%;
  margin: 0 auto;

  h1 {
    text-transform: capitalize;
    text-align: left;
    margin-bottom: 2rem;
  }
}

.grid-wrapper {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 1rem;

  @media screen and (max-width: 991px) {
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
