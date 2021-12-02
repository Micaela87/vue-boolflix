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
      },
    },
    // computed: {
    //   fullList() {
    //     return [...this.seriesList, ...this.movieList]
    //   }
    // },
    methods: {
        getMovies: async function() {
            try {
                let movies = await axios.get(`${this.urlMovie}?api_key=${this.apiKey}&language=it-IT&query=${this.param}`);
                let series = await axios.get(`${this.urlSeries}?api_key=${this.apiKey}&language=it-IT&query=${this.param}`)
                if (series.status === 200) {
                  this.seriesList = series.data.results.map((result) => {
                        let posterImgPath = this.handleMissingImg(result.poster_path);
                        let obj = {
                            id: result.id,
                            poster: `${posterImgPath}`,
                            originalTitle : result.original_name,
                            title: result.name,
                            overview: result.overview,
                            language: result.original_language,
                            rating: result.vote_average,
                            cast: ''
                        }

                        let seriesCast = [];
                        axios.get(`https://api.themoviedb.org/3/tv/${result.id}/credits?api_key=${this.apiKey}`)
                        .then((result) => {
                          for (let i = 0; i < 5; i++) {
                            seriesCast.push(result.data.cast[i].name);
                          }
                          let seriesCastToString = seriesCast.join(', ');
                          console.log('series', series)
                          obj.cast = seriesCastToString;
                        })
                        .catch((error) => {
                          console.log(error);
                        });
                        console.log(obj);
                        return obj;
                    });
                }

                if (movies.status === 200) {
                    this.movieList = movies.data.results.map((result) => {
                        
                        let posterImgPath = this.handleMissingImg(result.poster_path);

                        let obj = {
                            id: result.id,
                            poster: `${posterImgPath}`,
                            originalTitle : result.original_title,
                            title: result.title,
                            overview: result.overview,
                            language: result.original_language,
                            rating: result.vote_average,
                            cast: ''
                        }

                        let movieCast = [];
                        axios.get(`https://api.themoviedb.org/3/movie/${result.id}/credits?api_key=${this.apiKey}`)
                        .then((result) => {
                          for (let i = 0; i < 5; i++) {
                            movieCast.push(result.data.cast[i].name);
                          }
                          let movieCastToString = movieCast.join(', ');
                          obj.cast = movieCastToString;
                        })
                        .catch((error) => {
                          console.log(error);
                        });
                        console.log(obj);
                        return obj;
                    });
                }
                
            } catch(error) {
                console.log(error)
            }
            
            this.fullList = [...this.movieList, ...this.seriesList];
            console.log('full list', this.fullList)
        },
        resetSearch() {
          return this.fullList = [];
        },
        handleMissingImg(path) {
            let posterImgPath = '';
            if (path) {
              posterImgPath = `https://image.tmdb.org/t/p/w342${path}`
            } else {
              posterImgPath = 'https://via.placeholder.com/342.jpg?text=Immagine+non+disponibile'
            }

            return posterImgPath;
        },
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