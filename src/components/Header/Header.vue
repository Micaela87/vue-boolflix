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
          urlMovie: 'https://api.themoviedb.org/3/search/movie',
          urlSeries: 'https://api.themoviedb.org/3/search/tv',
          searchParam: 'ritorno al futuro',
          movieList: [],
          seriesList: []
      }
  },
//   created() {
//       this.getMovies();
//   },
  methods: {
      getMovies: async function() {
          try {
              let movies = await axios.get(`${this.urlMovie}?api_key=${this.apiKey}&language=it-IT&query=${this.searchParam}`);
              let series = await axios.get(`${this.urlSeries}?api_key=${this.apiKey}&language=it-IT&query=${this.searchParam}`)
              if (series.status === 200) {
                  console.log('serie tv', series);

                this.seriesList = series.data.results.map((result) => {
                      return {
                          originalTitle : result.original_name,
                          title: result.name,
                          language: result.original_language,
                          rating: result.vote_average
                      }
                  });
              }
              if (movies.status === 200) {
                  console.log('film', movies);

                  this.movieList = movies.data.results.map((result) => {
                      return {
                          originalTitle : result.original_title,
                          title: result.title,
                          language: result.original_language,
                          rating: result.vote_average
                      }
                  });
                //   console.log(this.movieList);
              }

              this.$emit('sendResults', [...this.movieList, ...this.seriesList]);
              
          } catch(error) {
              console.log(error)
          }
      },
  }
}
</script>

<style scoped lang="scss">

</style>