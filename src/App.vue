<template>
  <div id="app">
    <div v-if="$apollo.queries.categories.loading">
      <b-spinner
        variant="primary"
        class="mt-4"
      ></b-spinner>
    </div>
    <div v-else>
      <b-navbar
        toggleable="lg"
        type="light"
        variant="light"
      >
        <div class="nav-wrapper">
          <b-navbar-brand href="/">Magexo test</b-navbar-brand>

          <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>

          <b-collapse
            id="nav-collapse"
            is-nav
          >
            <b-navbar-nav>
              <template v-for="category in categories.children">
                <b-nav-item
                  v-if="category.children_count === '0'"
                  :key="category.uid"
                  :to="{ name: 'ProductsView', params: {name: nameEdit(category.name), id: category.uid, title: category.name}}"
                >{{ category.name }}
                </b-nav-item>
                <b-nav-item-dropdown
                  v-else
                  :key="category.uid"
                  :text="category.name"
                >
                  <b-dropdown-item
                    v-for="subcategory in category.children"
                    :key="subcategory.uid"
                    :to="{ name: 'ProductsView', params: {name: nameEdit(subcategory.name), id: subcategory.uid, title: subcategory.name }}"
                  >{{ subcategory.name }}</b-dropdown-item>
                  <b-dropdown-item :to="{ name: 'ProductsView', params: {name: nameEdit(category.name), id: category.uid, title: category.name}}">All {{ nameLower(category.name) }}</b-dropdown-item>
                </b-nav-item-dropdown>
              </template>
            </b-navbar-nav>
          </b-collapse>
        </div>
      </b-navbar>

      <main>
        <router-view />
      </main>
    </div>
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
    },
    nameLower (name) {
      return name.toLowerCase()
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
  background: #f9f9f9;
  min-height: 100vh;
}

main {
  padding: 3rem 1rem;

  .lingallery,
  .lingallery > figure {
    width: 100% !important;
  }
}

.navbar.bg-light {
  height: 80px;
  align-items: center;
  padding: 0 1rem;
  background-color: #fff !important;

  @media screen and (max-width: 991px) {
    height: auto;
    padding: 1rem !important;
  }

  .nav-wrapper {
    max-width: 1200px;
    width: 100%;
    display: flex;
    margin: 0 auto;

    @media screen and (max-width: 991px) {
      max-width: 100%;
      justify-content: space-between;
      flex-wrap: wrap;
    }
  }

  .navbar-collapse {
    justify-content: center;
  }

  .navbar-nav {
    @media screen and (max-width: 991px) {
      text-align: left;
    }
  }

  .nav-item {
    padding: 0 0.5rem;

    .nav-link {
      color: #081828 !important;
    }
  }
}
</style>
