<template>
  <div>
    <Post 
      v-for="post in posts" 
      :key="post.id" 
      :post="post" 
      @editPost="editPost" 
      @deletePost="deletePost"
    />
  </div>
</template>

<script>

import axios from 'axios'
import Post from "@/components/PostComponent.vue"

export default {
  data: function () {
    return {
      posts: []
    }
  },
  components: {
    Post
  },
  computed: {
  }, 
  methods: {
    async getPosts() {
      const posts = await axios.get('https://jsonplaceholder.typicode.com/posts')
      .then( resp => {
        this.posts = resp.data
      }).catch(e => console.log(e))
    },

    async editPost(editedPost) {
      const body = JSON.stringify(editedPost)
      const options = {
        headers: {
         "Content-type": "application/json; charset=UTF-8"
        }
      }
      
      let postResp

      debugger

      try {
        // Using POST doesn't work. Will give a 404 because no POST endpoint for the current resource has been set up.
        // Changing the url to `https://jsonplaceholder.typicode.com/posts` will work however will recieve status 201 for a newly created resource.
        // Since we are editing the resource (post) it is more logical that a PUT is used.

        // postResp = await axios.post(`https://jsonplaceholder.typicode.com/posts/${editedPost.id}`, body, options )
        // .then( resp => { return resp })
        // .catch(e => console.log(e))
        // console.log(postResp)

        postResp = await axios.put(`https://jsonplaceholder.typicode.com/posts/${editedPost.id}`, body, options )
        .then( resp => { return resp })
        .catch(e => console.log(e))
        console.log(postResp)
      } catch(err){
        console.log(err)
        return 500
      }

      if(postResp.status === 200){
        const foundPost = this.posts.find( p => p.id === editedPost.id)
        const indexOfPost = this.posts.indexOf(foundPost)

        debugger
        //replace post
        this.posts.splice(indexOfPost, 1, editedPost)
      }
    },

    async deletePost(id) {
      // console.log(id)
      let postResp

      try {
        postResp = await axios.delete(`https://jsonplaceholder.typicode.com/posts/${id}`)
          .then( resp => { return resp })
          .catch(e => console.log(e))

        console.log(postResp)
      } catch(err) {
        console.log(err)
        return err
      }

      postResp.status === 200 ? this.posts = this.posts.filter( t => t.id !== id) : console.log(postResp.status)

    }
  },
  created(){
    this.getPosts()
  }
}
</script>