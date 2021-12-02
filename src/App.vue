<template>
  <div id="app">
    <Header @sendParam="getSearchParam"/>
    <Main :param="searchParam" :active="searched"/>
  </div>
</template>

<script>
import Header from "./components/Header/Header.vue";
import Main from "./components/Main/Main.vue";

export default {
    name: "App",
    components: { 
      Header, 
      Main
    },
    data() {
        return {
            searchParam: '',
            searched: false
        };
    },
    methods: {
        getSearchParam(param) {
          // console.log(searchResults);
            this.searchParam = param;
            // console.log(this.movieList);
            // this.searched = searched;
            console.log(this.searchParam);
        }
    },
}
</script>

<style lang="scss">

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
</style>
