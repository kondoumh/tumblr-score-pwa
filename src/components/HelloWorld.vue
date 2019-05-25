<template>
  <div class="hello">
    <h1>Tumblr score</h1>
    <form @submit.prevent="fetchBlog">
      API Key: <input v-model="apiKey" placeholder="API Key"> Blog Identifier : <input v-model="blogIdentifier" placeholder="Blog Identifier">
      <button type="submit">fetch</button>
    </form>
    <p style="text-align: left"><a :href='url'>{{ title }}</a> {{ skip }} / {{ posts }} {{ fetching ? 'fetching..' : '' }}</p>
    <ul style="text-align: left;">
      <li v-for="item in items" v-bind:key="item.id">
        {{ item.date }} : {{ item.count }} : {{ item.type }} : <a :href='item.url'>{{ item.summary }}</a>
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
  data: () => ({
    apiKey: String,
    blogIdentifier: String,
    title: '',
    posts: 0,
    url: '',
    items: [],
    skip: 0,
    fetching: false
  }),
  methods: {
    async fetchBlog () {
      if (this.posts === 0) {
        const response = await fetch(`https://api.tumblr.com/v2/blog/${this.blogIdentifier}/info?api_key=${this.apiKey}`)
        const data = await response.json()
        const blog = data['response']['blog']
        this.title = blog['title']
        this.posts = blog['posts']
        this.url = blog['url']
      }
      this.skip += 100;
      this.fetching = true;
      const response2 = await fetch(`https://api.tumblr.com/v2/blog/${this.blogIdentifier}/posts/?notes_info=true&reblog_info=true&offset=${this.skip}&api_key=${this.apiKey}`)
      const data2 = await response2.json()
      data2['response']['posts'].forEach(post => {
        if (!post['reblogged_root_name']) {
          const postInfo = {
            id: post['id'],
            url: `https://${this.blogIdentifier}/post/${post['id']}`,
            date: `${post['date']}`.substring(0, 10),
            type: `${post['type']}`.substring(0, 1),
            slug: `${post['slug']}`,
            count: `${post['note_count']}`,
            summary: `${post['summary']}`.substring(0, 20)
          }
          if (!this.items.some(i => i.id == postInfo.id)) {
            this.items.push(postInfo)
          }
          this.fetching = false;
        }
      })
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
