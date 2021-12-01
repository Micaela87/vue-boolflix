<template>
  <div>
    <form action="">
        <input type="text" placeholder="Search a movie" v-model.trim="searchParam">
        <button @click.prevent="getMovies">Search</button>
    </form>
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
//   created() {
//       this.getMovies();
//   },
  methods: {
      getMovies: async function() {
          try {
              let response = await axios.get(`${this.url}?api_key=${this.apiKey}&language=it-IT&query=${this.searchParam}`);
              if (response.status === 200) {
                  console.log(response);
                  this.movieList = response.data.results.map((result) => {
                      return {
                          originalTitle : result.original_title,
                          title: result.title,
                          language: result.original_language,
                          rating: result.vote_average
                      }
                  });
                  console.log(this.movieList);
                  this.$emit('sendResults', this.movieList);
              }
              
          } catch(error) {
              console.log(error)
          }
      },
  }
}
</script>

<style scoped lang="scss">

</style>