<template>
  <div class="blog">
    <div class="blog-global-controls">
      <button class="new-post-button" :disabled="postUploading" v-on:click="toggleNewPost">{{!newPost?"Post New":"Post Cancel"}}</button>
      <select v-model="searchTarget">
        <option selected value="user">User</option>
        <option value="id">Post ID</option>
        <option value="text">Text</option>
      </select>
      <input v-model.trim="searchTerm" placeholder="Search..."/>
    </div>
    <form @submit="blogPostCreate" id="new-post-form" v-if="newPost">
      <label for="new-title-input">Title:</label>
      <input type="text" name="new-title-input" id="new-title-input" v-model="newPostData.title"/>
      <label for="new-body-textarea">Body:</label>
      <textarea type="text" name="id-new-body-textarea" id="new-body-textarea" v-model="newPostData.body"></textarea>
      <input id="new-post-submit" type="submit" value="Submit!" @click="validateNewPost">
    </form>
    <div v-if="loaded" class="post-wrapper">
      <blog-post
        v-on:blog-post-delete="blogPostDelete"
        v-on:blog-post-edit="blogPostEdit"
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
      searchTarget: "user",
      newPost: false,
      newPostData:{
        title:null,
        body:null,
        userId: 1 //Always use 1 for this demo. No particular reason.
      },
      postUploading:false
    }
  },
  //We need to have an ability to filter our posts, so we'll add a method that returns a new array of posts.
  computed:{
    filteredPosts(){
      if(this.searchTerm.length==0)
        return this.posts;

      let searchFun = ()=>true;
      switch(this.searchTarget){
        case "user": searchFun = (el)=>{return el.userId===Number(this.searchTerm)};break;
        case "id": searchFun = (el)=>{return el.id===Number(this.searchTerm)};break;
        case "text":{
          searchFun = (el)=>{
            let lowercaseTitle = el.title.toLowerCase();
            let lowercaseBody = el.body.toLowerCase();
            let lowercaseSearch = this.searchTerm.toLowerCase();
            return lowercaseTitle.includes(lowercaseSearch)||lowercaseBody.includes(lowercaseSearch);
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
              //Funny how increasingly likely -1 gets when you wait for a fetch and you doubleclick delete:)
              //Let's just pretend like nothing happened.
              if(pindex>=0)
                this.posts.splice(pindex,1);
            }
            else console.log("Error while deleting post#"+id);
          }//TODO: Should probably also include catch to handle rejected promises. Later,
          // Fetch API only rejects in case of network errors, so try not to have any for now.
      )
    },
    //FIXME: Not really something that /can/ be fixed here, but we can only add 1 post due to the server always responding with the same post ID.
    blogPostCreate: function(e){
      this.postUploading = true; //Used to disable anything that shouldn't be active while uploading
      fetch('https://jsonplaceholder.typicode.com/posts', {
        method: 'POST',
        body: JSON.stringify({
          title: this.newPostData.title,
          body: this.newPostData.body,
          userId: 1
        }),
        headers: {
          "Content-type": "application/json; charset=UTF-8"
        }
      })
          .then((res) =>{
            this.postUploading = false; //Got response, cancel uploading.
            if(res.status==201) { //201 All Good :)
              this.newPostData = {  // All good, clear the input form.
                title:null,
                body:null,
                userId: 1
              };
              this.newPost = false;
              return res.json()
            }
            else{
              console.log("Error while uploading new post with status code:" + res.status);
            }
          })
          .then((json)=>{
            if(!json) return;
            this.posts.splice(0,0,json); // Normally this would go to the bottom since the id is largest, but for showcase, we'll put it on top.
              //Perhaps we should've displayed the posts from largest id to smallest, but whatever, it's not that important now.
          });
      e.preventDefault(); // Honestly, reloading the page kinda breaks things. So don't reload.
    },
    toggleNewPost: function(){
      this.newPost=!this.newPost;
    },
    validateNewPost: function (e){
      console.log("Validating New Post Form!")
      if(this.newPostData.body===null||!this.newPostData.body.length
       ||this.newPostData.title===null||!this.newPostData.title.length) {
        e.preventDefault();
      }
    },
    blogPostEdit: function (id,json){
      let post = this.posts[this.getPostIndexByID(id)];
      post.title = json.title;
      post.body = json.body;
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
#new-title-input,#new-body-textarea,#new-post-form{
  display:block;
  box-sizing:border-box;
  width:95%;
  margin-left:auto;
  margin-right: auto;
}
#new-post-submit{
  display: block;
  margin:auto;
}
#new-post-form{
  width:100%;
}
</style>