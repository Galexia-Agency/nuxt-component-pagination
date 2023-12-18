# Galexia Nuxt Component Pagination

## Install

### Pre-Requisites

We assume you have a Nuxt project.

### You can now install this package

```bash
yarn add https://github.com/Galexia-Agency/nuxt-component-pagination
```

```js
// plugins/galexia/nuxt-components/pagination.js
import Vue from 'vue'
import Galexia_NuxtComponent_Pagination from 'nuxt-component-pagination/index.vue'

Vue.component('GalexiaPagination', Galexia_NuxtComponent_Pagination)
```

```js
// nuxt.config.js
...
plugins: [
  '~plugins/galexia/components/pagination.js'
],
...
```

### And use it like so

```js
// pages/index.js
<template>
  <GalexiaPagination
    :page-count="pageCount"
    :current-page="currentPage"
    :go-to-page="goToPage"
  />
</template>
<script>
  export default {
    data: {
      posts: {},
      pageCount: 5,
    }
    computed: {
      currentPage () {
        const p = this.$route.query.p
        const page = Array.isArray(p) ? p[0] : p
        return page ? parseInt(page) : 1
      }
    },
    methods: {
      goToPage (page) {
        this.$router.push({ path: this.$route.path, query: { ...this.$route.query, p: page.toString() } })
      }
    }
  }
</script>
```
