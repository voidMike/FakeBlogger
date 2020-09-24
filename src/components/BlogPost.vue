<template>
  <div>
   <div>User {{userID}}</div>
   <div>Post#{{postID}}</div>
   <div>{{postTitle}}</div>
   <div>{{postBody}}</div>
  </div>
</template>

<script>

export default {
  name: "BlogPost",
  props:{
    userID: Number,
    postID: Number,
    postTitle: String,
    postBody: String
  }
}
</script>

<style scoped>

</style>