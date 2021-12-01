<template>
  <div id="app">
    <Header @sendResults="getMovies"/>
    <Main :movies="movieList" :active="searched"/>
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
            movieList: [],
            searched: false
        };
    },
    methods: {
        getMovies(searchResults, searched) {
          console.log(searchResults);
            this.movieList = searchResults;
            console.log(this.movieList);
            this.searched = searched;
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
