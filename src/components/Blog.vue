<template>
  <div>
    <blog-post
      v-for="post in posts"
      :key = "post.postID"
      :userID = "post.userID"
      :postID = "post.postID"
      :postTitle = post.postTitle
      :postBody = "post.postBody"
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
  }
}
</script>

<style scoped>

</style>