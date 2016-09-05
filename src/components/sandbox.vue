<template>
  <div class="page" v-if="!error">
    <header class="header">
      <img class="logo" src="../assets/images/vue.png">
    </header>
    <nav class="navigation">
      <button v-on:click="getLinks()">Get new weekly!</button>
    </nav>
    <section class="posts__list">
      <article class="posts" v-for="post in posts | orderBy 'date' -1">
        <h2>Title: <em>{{post.title}}</em></h2>
        <p>
          <em>{{post.date}}</em>
        </p>
        <button v-on:click="toggleLinks(post)">Toggle Links</button>
        <div v-bind:class='{active: isActive(post)}'>
          <div v-for="postItem in post.body[0]" >
            <h4>Toppic: {{postItem.name}}</h4>
            <ul>
              <li v-for="linkItem in postItem.links">
                <strong><a href="{{linkItem.link}}">{{linkItem.title}}</a></strong>
                <p><em>{{linkItem.description}}</em></p>
              </li>
            </ul>
          </div>
        </div>
        <hr>
      </article>
    </section>
  </div>
  <h1 v-if="error">Error:</h1>
  <pre>{{error | json }}</pre>
</template>

<script>
export default {
  data () {
    return {
      msg: `Hi, vue-kit@${new Date().getFullYear()}`,
      posts: this.getBlogs(),
      error: '',
      showLinks: {}
    }
  },
  methods: {
    toggleLinks(item) {
      this.showLinks = item;
    },
    isActive(item) {
      return this.activeMessage === item
    },
    getBlogs() {
      this.$http.get('http://mrfrontend-weekly-api.herokuapp.com/blogs/all').then(function(response) {
        // success callback
        response.data.data.map(function (post) {
          let postToppicArray = Object.keys(post.body[0]).map((k) => post.body[0][k]);
          postToppicArray.map(function (itemToppic) {
            itemToppic.links.map(function (link) {
              link.html = writeLinkToHtml(link.title, link.link, link.description);
            });
          });
        });
        this.posts = response.data.data;
        console.log('posts: ', response);
      }, function(response) {
        // error callback
        this.error = response;
      })
    },
    getLinks() {
      this.$http.get('http://mrfrontend-weekly-api.herokuapp.com/blogs/fb').then((response) => {
        // success callback
//        this.posts = response.data.data;
//      console.log('links: ', response);
      }, (response) => {
        // error callback
      })
    }
  }
}

function writeLinkToHtml(title, link, description) {
  return '<li><a href="' + link + '" target="_blank" title="' + title + '">' + title + '</a><br /><em>' + description + '</em></li>';
}
</script>

<style lang="sass" scoped>
$color: #41B883;

.message {
  color: $color;
  margin: 30px;
}
.header,
.navigation {
  text-align: center;
}
.header {
  margin-bottom: 1rem;
}
.navigation {
  margin-bottom: 4rem;
}

img {
  max-width: 100%;
}
</style>
