# VuexRailsPlugin

### A Vuex plugin that easily maps Rails resources to Vuex modules

The idea of this plugin is to easily incorporate conventional rest endpoints defined when scaffolding Rails resources and map the responses to appropriate state in Vuex. ex. `resources :posts` generates the following endpoints:

 * GET|POST /posts
 * GET|PUT /posts/:id
 * DELETE /posts/:id

### Install

```bash
$ npm i vuex-rails-plugin
```

### Usage

The plugin can be imported into any Vuex store like so:

```js
// store.js
import Vuex from 'vuex'
import Vue from 'vue'
Vue.use(Vuex)
import VuexRailsPlugin from 'vuex-rails-plugin/src/VuexRailsPlugin'

export default new Vuex.Store({
  // ...
  plugins: [
    VuexRailsPlugin('posts'),
    VuexRailsPlugin('categories')
  ]
})
```

This example defines Vuex modules for 'posts' and 'categories' namespaces.
It supports the following state:

 * all - items returned from the index action
 * current - set when the show action is called
 * error - set if any errors occur such as validations
 
The state can be mapped to any Vue component using `mapState`

```js
// some-component.vue
import { mapState } from 'vuex'
export default {
  computed: {
    ...mapState('posts', {
      posts: state => state.all,
      currentPost: state => state.current
      error: state => state.error
    })
  }
}

```


Actions can also be called by using `mapActions`

```js
// some-component.vue
import { mapActions } from 'vuex'
export default {
  // ...
  methods: {
    ...mapActions('posts', {
      getPosts: 'getAll', // ex. this.getAll()
      getPost: 'get', // ex. this.getPost(id)
      createPost: 'create', // ex. this.createPost({title: 'Bad', body: 'Ass'})
      updatePost: 'update', // ex. this.updatePost({id: 1, title: 'Bad', body: 'Mama Jama'})
      destroyPost: 'destroy' // ex. this.destroyPost({})
    })
  }
}
```

Params are supported

```js
this.getPosts({page: 1, limit: 10})
```

Use directly in a form

```js
export default {
  data() {
    return {
      newPost: {
        title: '',
        body: ''
      }
    }
  }
}
```

```html
<form @submit.prevent="createPost(newPost)">
  <label>Title</label>
  <input type="text" v-model="newPost.title"/>
  <label>Body</label>
  <input type="text" v-model="newPost.body">
  <buton type="submit">Submit</buton>
</form>
```

Promises supported

```js
export default {
  // ...
  methods: {
    promiseMeThisPostWillCreate() {
      this.createPost(this.newPost)
        .then(createdPost => {
          alert('Thanks for keeping your promise!')
        })
        .catch(err => {
          alert('Why did you break my promise?')
        })
    }
  }
}
```

### TODO

* Support nested resources ex. `/categories/:category_id/posts`
* Support other options that may be necessary to support custom getters, filters, etc.
