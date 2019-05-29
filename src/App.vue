<template>
  <div id="app">
    <!-- <img alt="Vue logo" src="./assets/logo.png"> -->
    <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->
    <!-- <LeafletMapTest /> -->
    <DefaultLeafletComponent/>
  </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
// import LeafletMapTest from './components/LeafletMapTest.vue'
// import LeafletControlExample from './components/LeafletControlExample'
import DefaultLeafletComponent from './components/DefaultLeafletComponent'

export default {
  name: 'app',
  components: {
    // HelloWorld
    // LeafletMapTest
    // LeafletControlExample
    DefaultLeafletComponent
  }
}
</script>

<style>
body{
  padding: 0;
margin: 0;
}
</style>

