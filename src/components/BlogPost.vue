<template>
  <div class="blog-post">
   <div class="blog-post-header">
     <div class="user-id">User {{userId}}</div>
     <div class="post-id">Post#{{id}}</div>
   </div>
   <div v-if="!editing">
     <div class="blog-title">{{title}}</div>
     <div class="blog-body">{{body}}</div>
   </div>
   <form @submit="blogPostEdit" v-if="editing" class="edit-post-form">
     <label :for="'edit-title'+id">Title:</label>
     <input v-model="editedData.title" name="edit-title" v-bind:id="'edit-title'+id" class="edit-title"/>
     <label :for="'edit-body'+id">Body:</label>
     <textarea v-model="editedData.body" :id="'edit-body'+id" class="edit-body"></textarea>
     <input :id="'edit-save'+id" type="submit" value="Save!" class="edit-save" @click="validatePost" :disabled="updating"/>
   </form>
   <div class="blog-controls">
     <button @click="toggleEdit" :disabled="updating">{{ editing?"Cancel Edit":"Edit" }}</button>
     <button @click="$emit('blog-post-delete',id)" :disabled="updating"> Delete </button>
   </div>
  </div>
</template>

<script>

export default {
  name: "BlogPost",
  data: function(){
    return{
      editing:false,
      editedData:{
        title: this.title,
        body: this.body
      },
      updating:false
    }},
  props:{
    userId: Number,
    id: Number,
    title: String,
    body: String
  },
  methods:{
    blogPostEdit: function (e) {
      this.updating=true;
      fetch('https://jsonplaceholder.typicode.com/posts/1', {
        method: 'PUT',
        body: JSON.stringify({
          id: this.id,
          title: this.editedData.title,
          body: this.editedData.body,
          userId: this.userId
        }),
        headers: {
          "Content-type": "application/json; charset=UTF-8"
        }
      })
        .then((res) =>{
          this.updating=false;
          if(res.status==200) { // All Okay :)
            return res.json();
          }
          else{
            console.log("Error while editing post with status:" + res.status);
          }
        })
        .then((json) =>{
          if(!json) return;
            this.$emit("blog-post-edit",this.id,json);
            this.editing = false;
        })
        e.preventDefault();
      },
    validatePost: function (e){
      //Right now, all I care about if it's not empty, Vue JS handles escaping html tags.
      if(this.editedData.body===null||!this.editedData.body.length
          ||this.editedData.title===null||!this.editedData.title.length) {
        e.preventDefault(); // Stop the buttons default action.
      }
    },
    toggleEdit: function(){
      // Set or Reset the editedData before and after editing or canceling editing.
      // I felt it looked ugly when last edit remained in the form after canceling.
      this.editedData.title = this.title;
      this.editedData.body = this.body;
      this.editing=!this.editing;
    }
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
  padding:5px;
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
.edit-post-form{
  display:block;
  width:100%;
}
.edit-body, .edit-title{
  display:block;
  width:95%;
  margin-left:auto;
  margin-right: auto;
}
.edit-save{
  display:block;
  margin-left:auto;
  margin-right: auto;
}
</style>