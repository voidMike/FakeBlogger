<template>
  <div class="blog">
    <div v-if="loaded" class="post-wrapper">
      <blog-post
        v-on:blog-post-delete="blogPostDelete"
        v-for="post in posts"
        :key = "post.id"
        :userId = "post.userId"
        :id = "post.id"
        :title = "post.title"
        :body = "post.body"
      ></blog-post>
    </div>
    <div v-if="!loaded">
      Loading Posts...
    </div>
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
        {userId:1, id:0, title: "Title 0", body:"Hello World."}
      ],
      loaded:false
    }
  },
  // This works for now. Maybe one day we can lazy load, but (ironically) I'm too lazy to implement this.
  mounted: function (){
    fetch("https://jsonplaceholder.typicode.com/posts").then(response=>response.json())
        .then(json=>{this.posts=json; this.loaded=true;});
  },
  methods:{
    search: function(){

    },
    getPostIndexByID: function (id){

      let index = this.posts.findIndex(
          (elem)=>{return elem.id === id; }
      );
      console.log(index);
      return index;
    },
    blogPostDelete: function (id){
      this.posts.splice(this.getPostIndexByID(id),1);
    }
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