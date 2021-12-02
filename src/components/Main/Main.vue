<template>
  <main>
      <div class="results">
        <MovieCard
        v-for="(movie, i) in fullList" :key="i"
        :details="movie"/>
      </div>
  </main>
</template>

<script>
import axios from 'axios';
import MovieCard from "./MovieCard.vue";

export default {
    name: "Main",
    components: {
        MovieCard
    },
      props: {
          param: String,
    },
    data() {
      return {
          apiKey: '51568f4302a2904751f1dfa9123f0199',
          urlMovie: 'https://api.themoviedb.org/3/search/movie',
          urlSeries: 'https://api.themoviedb.org/3/search/tv',
          movieList: [],
          seriesList: [],
          fullList: []
      }
    },
    watch: {
      param(newValue) {
        if (newValue) {
          this.getMovies();
        } else {
          this.resetSearch();
        }
      }
    },
    methods: {
        getMovies: async function() {
            try {
                let movies = await axios.get(`${this.urlMovie}?api_key=${this.apiKey}&language=it-IT&query=${this.param}`);
                let series = await axios.get(`${this.urlSeries}?api_key=${this.apiKey}&language=it-IT&query=${this.param}`)
                if (series.status === 200) {

                  this.seriesList = series.data.results.map((result) => {
                        return {
                            poster: `https://image.tmdb.org/t/p/w342${result.poster_path}`,
                            originalTitle : result.original_name,
                            title: result.name,
                            overview: result.overview,
                            language: result.original_language,
                            rating: result.vote_average
                        }
                    });
                }
                if (movies.status === 200) {

                    this.movieList = movies.data.results.map((result) => {
                        return {
                            poster: `https://image.tmdb.org/t/p/w342${result.poster_path}`,
                            originalTitle : result.original_title,
                            title: result.title,
                            overview: result.overview,
                            language: result.original_language,
                            rating: result.vote_average
                        }
                    });
                }

                return this.fullList = [...this.movieList, ...this.seriesList];
                
            } catch(error) {
                console.log(error)
            }
        },
        resetSearch() {
          return this.fullList = [];
        }
    }
}
</script>

<style scoped lang="scss">
    main {
        background-color: grey;
    }

    .results {
        display: flex;
        flex-wrap: wrap;
        justify-content: space-between;
        align-items: center;
        min-height: calc(100vh - 10rem);
        width: 100%;
    }

    h2 {
        width: 100%;
        text-align: center;
        font-size: 2rem;
    }
</style>