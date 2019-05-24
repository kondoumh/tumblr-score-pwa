<template>
  <div class="hello">
    <h1>Tumblr score</h1>
    <form @submit.prevent="fetchBlog">
      <input v-model="apiKey" placeholder="API Key"><br>
      <input v-model="blogIdentifier" placeholder="Blog Identifier"><br>
      <button type="submit">submit</button>
      <p>Title : {{ title }}</p>
      <p>Posts : {{ posts }}</p>
      <p>URL : {{ url }}</p>
    </form>
    <ul>
      <li v-for="item in items" v-bind:key="item.id">
        {{ item.url }}
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  mounted () {
    if (localStorage.apiKey) {
      this.apiKey = localStorage.apiKey
    }
    if (localStorage.blogIdentifier) {
      this.blogIdentifier = localStorage.blogIdentifier
    }
  },
  props: {
    apiKey: String,
    blogIdentifier: String
  },
  data: () => ({
    title: '',
    posts: 0,
    url: '',
    items: []
  }),
  methods: {
    async fetchBlog () {
      const response = await fetch(`https://api.tumblr.com/v2/blog/${this.blogIdentifier}/info?api_key=${this.apiKey}`)
      const data = await response.json()
      const blog = data['response']['blog']
      this.title = blog['title']
      this.posts = blog['posts']
      this.url = blog['url']
      const response2 = await fetch(`https://api.tumblr.com/v2/blog/${this.blogIdentifier}/posts/?notes_info=true&reblog_info=true&offset=20&api_key=${this.apiKey}`)
      const data2 = await response2.json()
      Object.keys(data2['response']['posts']).forEach(post => {
        const postInfo = {
          id: post['id'],
          url: `'https://${this.blogIdentifier}/post/${post['id']}'`,
          date: `'${post['date']}'`,
          type: `'${post['type']}'`,
          slug: `'${post['slug']}'`,
          count: `'${post['note_count']}'`
        }
        this.items.push(postInfo)
      })
      console.log(data2)
      localStorage.apiKey = this.apiKey
      localStorage.blogIdentifier = this.blogIdentifier
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
