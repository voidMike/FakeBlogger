<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <Blog></Blog>
  </div>
</template>

<script>
import Blog from "@/components/Blog";

export default {
  name: 'App',
  components: {
    Blog
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  display:flex;
  flex-direction: column;
  align-items: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
