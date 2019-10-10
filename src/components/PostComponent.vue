<template>
  <div class="container">
    <div class="content">
      <form @submit.prevent="saveEdit" method="POST" action="#">
        <label for="title">Title:</label>
        <input 
          name="title" 
          :value="post.title"
          :disabled="isDisabled"
          required>
        <label for="body">Content:</label>
        <textarea 
          name="body" 
          :value="post.body.replace(/\n/g, '')" 
          :disabled="isDisabled" 
          required/>
      <button v-show="isDisabled" type="button" @click="editThisPost">edit</button>
      <button v-show="!isDisabled" type="submit">save</button>
      <button type='button' @click="deleteThisPost(post.id)">delete</button>
      </form>
    </div>
  </div>
</template>
<script>
export default {
  data: function () {
    return {
      isDisabled: true
    }
  },
  props: ['post'],
  methods: {
    toggleDisabled(){
      this.isDisabled = !this.isDisabled
    },
    deleteThisPost(id){
      this.$emit('deletePost',id)
    },
    editThisPost(){
      this.toggleDisabled()
    },
    saveEdit(event){
      const editedPost = { ... this.post }

      // compare changes
      const titleHasChanged = this.post.title !== event.currentTarget.title.value  
      const bodyHasChanged = this.post.body !== event.currentTarget.body.value
      const formValid = event.currentTarget.checkValidity() === true
       

      if ((titleHasChanged || bodyHasChanged) && formValid){
        editedPost.title = event.currentTarget.title.value  
        editedPost.body = event.currentTarget.body.value
        this.$emit('editPost', editedPost)
      }
    }
  },
  watch:{
    post(){
      this.toggleDisabled()
    }
  }
}
</script>
<style>
 .container {
    border-width: 1px;
    border-style: solid;
    padding: 2px;
    text-align: center;
    margin: 2px;
    width: fit-content;
    display: inline-flex;
    line-height: inherit;
 }

 .title{
    width: 100%;
    text-align: left;
    display: inline-flex;
    line-height: inherit;
 }

 .content {
    padding-left: 5px;
    padding-right: 5px;
    text-align: left;
    display: block;
    width: 100%;
    height: -webkit-fill-available;
 }

  input {
    width: 100%;
    font-weight: bold;
    border-style: none;
  }

  textarea {
    width: -webkit-fill-available;
    height: -webkit-fill-available;
    max-height: 60px;
    height: 60px;
    resize: none;
    border-style: none;
  }
</style>