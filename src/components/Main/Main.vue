<template>
  <main>
    <form>
        <label for="genre">Sort Movies by Genre</label>
        <select ref="movies" name="genre" id="genre" v-model="selectedMovieGenre">
            <option value="" selected>All</option>
            <option :value="genre.name" v-for="(genre, i) in movieGenres" :key="i">{{ genre.name }}</option>
        </select>
        <label for="genre">Sort Series by Genre</label>
        <select ref="series" name="genre" id="genre" v-model="selectedSeriesGenre">
            <option value="" selected>All</option>
            <option :value="genre.name" v-for="(genre, i) in seriesGenres" :key="i">{{ genre.name }}</option>
        </select>
    </form>
      <div class="results">
        <MovieCard
        v-for="(movie, i) in filteredResults" :key="i"
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
        MovieCard,
    },
      props: {
          param: String,
    },
    data() {
      return {
          apiKey: '51568f4302a2904751f1dfa9123f0199',
          baseUrl: 'https://api.themoviedb.org/3/search/',
          fullList: [],
          movieGenres: [],
          seriesGenres: [],
          allGenres: [],
          selectedMovieGenre: '',
          selectedSeriesGenre: '',
      }
    },
    computed: {
      filteredResults() {
        if (!this.selectedGenre) {
          return this.fullList;
        }
        
        return this.fullList.filter((movie) => {
          if (movie.genres.includes(this.selectedGenre)) {
            return movie;
          }
        })
      },
      selectedGenre() {
        if (this.selectedMovieGenre) {
          return this.selectedMovieGenre;
        } else {
          return this.selectedSeriesGenre;
        }
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
      selectedMovieGenre(newValue) {
        if (newValue) {
          this.$refs.series.setAttribute('disabled', true);
        } else {
          this.$refs.series.removeAttribute('disabled');
        }
      },
      selectedSeriesGenre(newValue) {
        if (newValue) {
          this.$refs.movies.setAttribute('disabled', true);
        } else {
          this.$refs.movies.removeAttribute('disabled');
        }
      }
    },
    created() {
      this.getAllGenres();
    },
    methods: {
        getMovies: async function() {
            try {
                let parameters = {
                    api_key: `${this.apiKey}`,
                    language: 'it-IT',
                    query: `${this.param}`
                }
                // calls to get movies and series
                let movies = await axios.get(`${this.baseUrl}movie`, {
                  params: parameters
                });
                let series = await axios.get(`${this.baseUrl}tv`, {
                  params: parameters
                });

                let seriesList = [],
                    movieList = [];
                // saves results into seriesList array
                if (series.status === 200) {
                  seriesList = series.data.results;
                }
                
                // saves results into movieList array
                if (movies.status === 200) {
                  movieList = movies.data.results;
                }

                // returns a list of objects with only the necessary properties
                return this.fullList = [...movieList, ...seriesList].map((result) => {
                  let posterImgPath = this.handleMissingImg(result.poster_path),
                      title = result.title ? result.title : result.name,
                      originalTitle = result.original_title ? result.original_title : result.original_name,
                      category = result.title ? 'movie' : 'tv',
                      overview = result.overview ? result.overview : 'Overview non disponibile',
                      language = '';

                  if (result.original_language === 'it' || result.original_language === 'en') {
                    let src = require(`@/assets/img/${result.original_language}.png`);
                    language = `<img style="width: 100%" src=${src} alt="${result.original_language}">`
                  } else {
                    language = `<span>${result.original_language.toUpperCase()}</span>`
                  }

                  let obj = {
                      poster: `${posterImgPath}`,
                      originalTitle,
                      title,
                      overview,
                      language,
                      rating: result.vote_average,
                      cast: '',
                      genres: '',
                      category
                  }

                  axios.get(`https://api.themoviedb.org/3/${category}/${result.id}/credits?api_key=${this.apiKey}`)
                        .then((result) => {
                          obj.cast = this.handleActors(result);
                        })
                        .catch((error) => {
                          console.log(error);
                        });

                  obj.genres = this.handleGenres(result.genre_ids);

                  return obj;
                });
                
            } catch(error) {
                console.log(error)
            }
        },
        resetSearch() {
          return this.fullList = [];
        },
        handleMissingImg(path) {
            if (path) {
              return `https://image.tmdb.org/t/p/w342${path}`
            } else {
              return 'https://via.placeholder.com/342.jpg?text=Immagine+non+disponibile'
            }
        },
        handleActors(result) {
          let cast = [];
          for (let i = 0; i < 5; i++) {
              if (!result.data.cast[i]) {
                break;
              }
              cast.push(result.data.cast[i].name);
          }

          if (!cast.length) {
            return 'Cast non disponibile';
          }
          
          return cast.join(', ');
        },
        handleGenres(genresArr) {
          let genresNames = [];
              this.allGenres.forEach((genre) => {
              for (let i = 0; i < genresArr.length; i++) {
                if (genre.id === genresArr[i] && !genresNames.includes(genre.name)) {
                  genresNames.push(genre.name);
                }
              }
            });
          
          if (!genresNames.length) {
            return 'Genere non disponibile';
          }

          return genresNames.join(', ');
        },
        getAllGenres: async function() {
          let allMovieGenres = await axios.get(`https://api.themoviedb.org/3/genre/movie/list?api_key=${this.apiKey}`);
          let allSeriesGenres = await axios.get(`https://api.themoviedb.org/3/genre/tv/list?api_key=${this.apiKey}`);
          this.seriesGenres = allSeriesGenres.data.genres;
          this.movieGenres = allMovieGenres.data.genres;
          return this.allGenres = [...this.seriesGenres, ...this.movieGenres]
        },
    }
}
</script>

<style scoped lang="scss">
    main {
        background-color: grey;
    }

    form {
      padding: 2rem;
    }

    label {
      font-size: 1.6rem;
      margin: 0 1rem;
    }

    option, select {
      font-size: 1.6rem;
      font-family: 'Roboto', sans-serif;
    }

    .results {
        display: flex;
        flex-wrap: wrap;
        justify-content: space-between;
        align-items: center;
        min-height: calc(100vh - 10rem);
        width: 100%;
    }
</style>