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
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
    apiKey: String,
    blogIdentifier: String
  },
  data: () => ({
    title: '',
    posts: 0,
    url: ''
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
      console.log(data2)
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
