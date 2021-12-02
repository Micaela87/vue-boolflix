<template>
  <main>
      <div class="results">
        <MovieCard
        v-for="(movie, i) in fullList" :key="i"
        :details="movie"/>
      <!-- </div>
      <div class="results" v-if="!this.movies.length && this.active">
        <h2>La ricerca non ha prodotto risultati</h2>
      </div>
      <div class="results" v-if="!this.active">
        <h2>Usa la barra di ricerca in alto per trovare il tuo film preferito</h2> -->
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
          // active: Boolean
    },
    data() {
      return {
          apiKey: '51568f4302a2904751f1dfa9123f0199',
          urlMovie: 'https://api.themoviedb.org/3/search/movie',
          urlSeries: 'https://api.themoviedb.org/3/search/tv',
          // searchParam: this.param,
          movieList: [],
          seriesList: [],
          fullList: [],
          searched: false
      }
    },
    watch: {
      param(newValue) {
        if (newValue) {
          console.log(newValue);
          this.getMovies();
        }
      }
    },
    methods: {
        getMovies: async function() {
          // console.log(this.searchParam);
          if (this.param) {
            try {
                let movies = await axios.get(`${this.urlMovie}?api_key=${this.apiKey}&language=it-IT&query=${this.param}`);
                let series = await axios.get(`${this.urlSeries}?api_key=${this.apiKey}&language=it-IT&query=${this.param}`)
                if (series.status === 200) {
                    console.log('serie tv', series);

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
                    console.log('film', movies);

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
                  //   console.log(this.movieList);
                }

                this.searched = true;

                return this.fullList = [...this.movieList, ...this.seriesList];
                
            } catch(error) {
                console.log(error)
            }
          }
        },
        getSearchParam() {
            this.searchParam = this.param;
            this.getMovies();
            return true;
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