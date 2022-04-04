<template>
  <div>
    <products
      :id="id"
      :title="title"
    ></products>
  </div>
</template>

<script>
import Products from '@/components/Products.vue'
export default {
  name: 'ProductsView',
  components: {
    Products
  },
  watch: {
    '$route.params.id': function () {
      this.id = this.$route.params.id
      this.title = this.$route.params.title
    }
  },
  data () {
    return {
      id: this.$route.params.id,
      title: this.$route.params.name
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
