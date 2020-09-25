<template>
  <div class="blog-post">
   <div class="user-id">User {{userId}}</div>
   <div class="post-id">Post#{{id}}</div>
   <div class="blog-title">{{title}}</div>
   <div class="blog-body">{{body}}</div>
  </div>
</template>

<script>

export default {
  name: "BlogPost",
  props:{
    userId: Number,
    id: Number,
    title: String,
    body: String
  }
}
</script>

<style scoped>
.blog-post{
  background-color:#2c3e50;
  color:white;
  max-width:600px;
  border:3px solid #42b983;
}
.blog-body{
  border: 1px solid #42b983;
  text-align: left;
}
.blog-title{
  font-size: 1.2em;
}
.user-id,.post-id{
  display: inline-block;

}

</style>