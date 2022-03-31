<template>
  <div id="app">
    <nav>
      <div
        class="menu-item"
        v-for="category in categories.children"
        :key="category.uid"
      >
        <router-link :to="{ name: 'ProductsView', params: {name: nameEdit(category.name), id: category.uid}}"> {{ category.name }}
          <span
            class="arrow"
            v-if="category.children_count > 0"
          >
            <font-awesome-icon icon="fa-solid fa-angle-down" />
          </span>
        </router-link>
        <div
          class="submenu"
          v-if="category.children_count > 0"
        >
          <div
            class="menu-item"
            v-for="subcategory in category.children"
            :key="subcategory.uid"
          >
            <router-link :to="{ name: 'ProductsView', params: {name: nameEdit(subcategory.name), id: subcategory.uid }}"> {{ subcategory.name }}
            </router-link>
          </div>
        </div>
      </div>
    </nav>
    <router-view />
  </div>
</template>

<script>
import gql from 'graphql-tag'

const CATEGORIES = gql`
  query {
    categories(pageSize: 1, currentPage: 1) {
      total_count
      items {
        uid
        level
        name
        path
        children_count
        children {
          uid
          name
          path
          children_count
          children {
            uid
            level
            name
            path
          }
        }
      }
    }
  }
`

export default {
  name: 'AppView',
  apollo: {
    categories: {
      query: CATEGORIES,
      update: (data) => data.categories.items[0]
    }
  },
  methods: {
    nameEdit (name) {
      return name.split(/[ ,]+/).join('-').toLowerCase()
    }
  },
  data: () => ({
    categories: []
  }),
  mounted () {}
}
</script>

<style lang="scss">
@import url("https://fonts.googleapis.com/css2?family=Roboto:wght@400;500;700&display=swap");
#app {
  font-family: "Roboto", sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
}

nav {
  height: 5rem;
  padding: 0 1.5rem;
  border-bottom: 0.0625rem solid #e7e7e7;
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;

  .menu-item {
    padding: 0 1rem;
    display: flex;
    align-items: center;
    height: 100%;
    .submenu {
      z-index: 100;
      display: none;
      background: #fff;
      position: absolute;
      top: 100%;
      left: 0;
      right: 0;
      padding: 1.875rem;
      border-bottom: 0.0625rem solid #e7e7e7;
      border-top: 0.0625rem solid #e7e7e7;
      justify-content: center;
    }

    &:hover > .submenu,
    .submenu:hover {
      display: flex;
    }
  }

  a {
    font-size: 1.125rem;
    color: rgb(23, 32, 38);
    text-decoration: none;

    .arrow {
      margin-left: 0.25rem;
    }
  }
}
</style>
