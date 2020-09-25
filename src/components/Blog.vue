<template>
  <div class="blog">
    <blog-post
      v-for="post in posts"
      :key = "post.id"
      :userId = "post.userId"
      :id = "post.id"
      :title = "post.title"
      :body = "post.body"
    ></blog-post>
  </div>
</template>

<script>
import BlogPost from "@/components/BlogPost";
export default {
  name: "Blog",
  components:{BlogPost},
  data: function(){
    return {
      posts:[
        {userID:1, postID:0, postTitle: "Title 0", postBody:"Hello World."},
        {userID: 1,postID: 1, postTitle: "Big Day Today, Freeman", postBody: "Do you know who ate all the doughnuts?"},
        {userID: 1,postID: 2, postTitle: "Unpopular Opinion", postBody: "Why do we all have to wear these ridiculous ties?"}
      ]
    }
  },
  // This works for now. Maybe one day we can lazy load, but (ironically) I'm too lazy to implement this.
  mounted: function (){
    fetch("https://jsonplaceholder.typicode.com/posts").then(response=>response.json())
        .then(json=>{this.posts=json;});
  }
}
</script>

<style scoped>
.blog{
  display:flex;
  flex-direction: column;
  align-items: center;
  background-color: aliceblue;
}
</style>