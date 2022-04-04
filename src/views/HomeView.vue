<template>
  <div>
    <products :id="defaultId"></products>
  </div>
</template>

<script>
import gql from 'graphql-tag'
import Products from '@/components/Products.vue'

const DEFAULT_CATEGORY = gql`
  query {
    categories(pageSize: 1) {
      total_count
      items {
        uid
        children_count
      }
    }
  }
`

export default {
  name: 'HomeView',
  apollo: {
    defaultId: {
      query: DEFAULT_CATEGORY,
      update: (data) => data.categories.items[0].uid
    }
  },
  components: {
    Products
  },
  data: () => ({
    defaultId: ''
  })
}
</script>
