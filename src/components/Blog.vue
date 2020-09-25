<template>
  <div class="blog">
    <div class="blog-global-controls">
      <button class="new-post-button">Post New</button>
      <select v-model="searchTarget">
        <option selected value="user">User</option>
        <option value="id">Post ID</option>
        <option value="text">Text</option>
      </select>
      <input v-model="searchTerm" placeholder="Search..."/>
    </div>
    <div v-if="loaded" class="post-wrapper">
      <blog-post
        v-on:blog-post-delete="blogPostDelete"
        v-for="post in filteredPosts"

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
      loaded:false,
      searchTerm: "",
      searchTarget: "user"
    }
  },
  //We need to have an ability to filter our posts, so we'll add a method that returns a new array of posts.
  computed:{
    filteredPosts(){
      let searchTerm = this.getSearchTerm();
      if(searchTerm.length==0)
        return this.posts;

      let searchFun = ()=>true;
      switch(this.searchTarget){
        case "user": searchFun = (el)=>{return el.userId===Number(searchTerm)};break;
        case "id": searchFun = (el)=>{return el.id===Number(searchTerm)};break;
        case "text":{
          searchFun = (el)=>{
            return el.title.includes(searchTerm)||el.body.includes(searchTerm);
          }
          break;
        }
      }

      return this.posts.filter(searchFun);
    }
  },
  // This works for now. Maybe one day we can lazy load, but (ironically) I'm too lazy to implement this.
  // Would technically need to check if the code is 200 before actually loading.
  mounted: function (){
    fetch("https://jsonplaceholder.typicode.com/posts").then(response=>response.json())
        .then(json=>{this.posts=json; this.loaded=true;});
  },
  methods:{
    getSearchTerm: function(){
      return this.searchTerm.trim();
    },
    getPostIndexByID: function (id){

      let index = this.posts.findIndex(
          (elem)=>{return elem.id === id; }
      );
      console.log(index);
      return index;
    },
    blogPostDelete: function (id){
      //First we ask the server to delete the post and then
      //wait for confirmation before actually removing it from the page.
      fetch('https://jsonplaceholder.typicode.com/posts/id', {
        method: 'DELETE',
      }).then(
          (res)=>{
            if(res.status==200)
            {
              let pindex = this.getPostIndexByID(id);
              //Funny how increasingly likely -1 is when you wait for a fetch and you doubleclick delete:)
              //For now, let's just pretend like nothing happened.
              if(pindex>=0)
                this.posts.splice(pindex,1);
            }
            else console.log("Error while deleting post#"+id);
          }//TODO: Should probably also include catch to handle rejected promises. Later,
          // Fetch API only rejects in case of network errors, so try not to have any for now.
      )
    }
  }
}
</script>

<style scoped>
.blog{
  width:600px;
  display:flex;
  flex-direction: column;
  align-items: center;
  background-color: aliceblue;
}
.blog-global-controls{
  display:flex;
  justify-content: right;
  width: 100%;
}
.new-post-button{
  margin-right: auto;
}
</style>