<template>
  <div>
    
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'Header',
  data() {
      return {
          apiKey: '51568f4302a2904751f1dfa9123f0199',
          url: 'https://api.themoviedb.org/3/search/movie',
          searchParam: 'ritorno al futuro',
          movieList: []
      }
  },
  created() {
      this.getMovies();
  },
  methods: {
      getMovies: async function() {
          try {
              let response = await axios.get(`${this.url}?api_key=${this.apiKey}&query=${this.searchParam}`);
              console.log(response);
              
          } catch(error) {
              console.log(error)
          }
      }
  }
}
</script>

<style scoped lang="scss">

</style>