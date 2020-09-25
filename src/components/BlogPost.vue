<template>
  <div class="blog-post">
   <div class="blog-post-header">
     <div class="user-id">User {{userId}}</div>
     <div class="post-id">Post#{{id}}</div>
   </div>
   <div class="blog-title">{{title}}</div>
   <div class="blog-body">{{body}}</div>
   <div class="blog-controls">
     <button>Edit</button>
     <button @click="$emit('blog-post-delete',id)"> Delete </button>
   </div>
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
  width:600px;
  box-sizing: border-box;
  border:3px solid #42b983;
  margin-bottom: 10px;
}
.blog-body{
  border-bottom: 1px solid #42b983;
  border-top:1px solid #42b983;
  text-align: left;
}
.blog-title{
  font-size: 1.2em;
}
.user-id,.post-id{
  display: inline-block;
  padding:5px;
  padding-left: 2px;
}
.post-id{
  color: #7c8ea0;
}
.blog-post-header{
  display:flex;
  justify-content: space-between;
}

</style>