<template>
  <div id="app">
    <!-- test1 -->
    <!-- <GetTest></GetTest> -->
    <!-- <img src="./assets/logo.png"> -->
    <!-- <router-view/> -->
    <WordInput></WordInput>a
  </div>
</template>

<script>
// import GetTest from './components/GetTest.vue';
import WordInput from './components/WordInput.vue';
export default {
  name: 'App',
  components:{
    WordInput
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
