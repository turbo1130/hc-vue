<template>
  <div id="app">
    <SearchPage/>
  </div>
</template>

<script>
import SearchPage from './components/SearchPage.vue'

export default {
  name: 'App',
  components: {
    SearchPage
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
}
  body{
    padding: 0;
    margin: 0;
  }

/*设置全局滚动条*/
::-webkit-scrollbar {
  width: 8px;
  border-left: 1px solid;
  border-color: rgba(0, 0, 0, 0.1);
}
::-webkit-scrollbar-thumb {
  background-color: rgba(0, 0, 0, 0.2);
  border-radius: 20px;
  overflow-x: hidden;
}
</style>
