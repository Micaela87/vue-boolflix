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
          urlMovie: 'https://api.themoviedb.org/3/search/movie',
          urlSeries: 'https://api.themoviedb.org/3/search/tv',
          movieList: [],
          seriesList: [],
          fullList: [],
          movieGenres: [],
          seriesGenres: [],
          selectedMovieGenre: '',
          selectedSeriesGenre: '',
          selectedGenre: ''
      }
    },
    computed: {
      filteredResults() {
        console.log(this.selectedGenre);
        if (!this.selectedGenre) {
          return this.fullList;
        }
        
        return this.fullList.filter((movie) => {
          if (movie.genres.includes(this.selectedGenre)) {
            return movie;
          }
        })
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
          this.selectedGenre = newValue;
        } else {
          this.$refs.series.removeAttribute('disabled');
          this.selectedGenre = newValue;
        }
      },
      selectedSeriesGenre(newValue) {
        if (newValue) {
          this.$refs.movies.setAttribute('disabled', true);
          this.selectedGenre = newValue;
        } else {
          this.$refs.movies.removeAttribute('disabled');
          this.selectedGenre = newValue;
        }
      }
    },
    created() {
      this.getAllMovieGenres();
      this.getAllSeriesGenres();
    },
    methods: {
        getMovies: async function() {
            try {
                // calls to get movies and series
                let movies = await axios.get(`${this.urlMovie}?api_key=${this.apiKey}&language=it-IT&query=${this.param}`);
                let series = await axios.get(`${this.urlSeries}?api_key=${this.apiKey}&language=it-IT&query=${this.param}`);

                // creates an object with only the properties needed for TV series
                if (series.status === 200) {
                  this.seriesList = series.data.results.map((result) => {
                        
                        // handles img in case of poster missing
                        let posterImgPath = this.handleMissingImg(result.poster_path);

                        // object with the necessary properties
                        let obj = {
                            poster: `${posterImgPath}`,
                            originalTitle : result.original_name,
                            title: result.name,
                            overview: result.overview,
                            language: result.original_language,
                            rating: result.vote_average,
                            cast: '',
                            genres: ''
                        }

                        // call to get actors of the movie
                        axios.get(`https://api.themoviedb.org/3/tv/${result.id}/credits?api_key=${this.apiKey}`)
                        .then((result) => {
                          let castList = this.handleActors(result);
                          obj.cast = castList;
                        })
                        .catch((error) => {
                          console.log(error);
                        });

                        let genres = this.handleSeriesGenres(result.genre_ids);
                        obj.genres = genres;

                        console.log(obj);
                        return obj;
                    });
                }

                // creates an object with only the necessary properties for movies
                if (movies.status === 200) {
                    this.movieList = movies.data.results.map((result) => {
                        
                        // console.log(result);

                        // handles img in case of poster missing
                        let posterImgPath = this.handleMissingImg(result.poster_path);

                        // object with necessary properties
                        let obj = {
                            poster: `${posterImgPath}`,
                            originalTitle : result.original_title,
                            title: result.title,
                            overview: result.overview,
                            language: result.original_language,
                            rating: result.vote_average,
                            cast: '',
                            genres: ''
                        }

                        // call to get actors
                        axios.get(`https://api.themoviedb.org/3/movie/${result.id}/credits?api_key=${this.apiKey}`)
                        .then((result) => {
                          let castList = this.handleActors(result);
                          obj.cast = castList;
                        })
                        .catch((error) => {
                          console.log(error);
                        });

                        let genres = this.handleMovieGenres(result.genre_ids);
                        obj.genres = genres;

                        // console.log(obj);
                        return obj;
                    });
                }
                
            } catch(error) {
                console.log(error)
            }

            return this.fullList = [...this.movieList, ...this.seriesList];
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
        handleActors(result) {
          let cast = [];
          for (let i = 0; i < 5; i++) {
              cast.push(result.data.cast[i].name);
            }
            let castToString = cast.join(', ');
            return castToString;
        },
        handleMovieGenres(genresArr) {
          let genresNames = [];
          this.movieGenres.forEach((genre) => {
            for (let i = 0; i < genresArr.length; i++) {
              if (genre.id === genresArr[i]) {
                genresNames.push(genre.name);
              }
            }
          });
          return genresNames.join(', ');
        },
        handleSeriesGenres(genresArr) {
          let genresNames = [];
          this.seriesGenres.forEach((genre) => {
            for (let i = 0; i < genresArr.length; i++) {
              if (genre.id === genresArr[i]) {
                genresNames.push(genre.name);
              }
            }
          });
          return genresNames.join(', ');
        },
        getAllMovieGenres: async function() {
          let allMovieGenres = await axios.get(`https://api.themoviedb.org/3/genre/movie/list?api_key=${this.apiKey}`);
          this.movieGenres = allMovieGenres.data.genres;
          console.log('generi', this.movieGenres);
        },
        getAllSeriesGenres: async function() {
          let allSeriesGenres = await axios.get(`https://api.themoviedb.org/3/genre/tv/list?api_key=${this.apiKey}`);
          this.seriesGenres = allSeriesGenres.data.genres;
          console.log('generi', this.seriesGenres);
        }
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