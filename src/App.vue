<template>
  <div id="app">
    <Header @sendParam="getSearchParam"/>
    <Main :param="searchParam"/>
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
        };
    },
    methods: {
        getSearchParam(param) {
            this.searchParam = param;
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
