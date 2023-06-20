<template>
  <div id="app">
    <img  alt="Swagger logo" width="30%" src="./assets/swagger.jpg">
    <HelloWorld class="main-div" />
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'App',
  components: {
    HelloWorld
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.main-div{
  padding-top: 1.5rem;
}
</style>
