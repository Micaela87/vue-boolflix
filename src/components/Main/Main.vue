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
          fullList: [],
          genres: []
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
    created() {
      this.getAllGenres();
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

                        let genres = this.handleGenres(result.genre_ids);
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

                        let genres = this.handleGenres(result.genre_ids);
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
        handleGenres(genresArr) {
          let genresNames = [];
          this.genres.forEach((genre) => {
            for (let i = 0; i < genresArr.length; i++) {
              if (genre.id === genresArr[i]) {
                genresNames.push(genre.name);
              }
            }
          });
          return genresNames.join(', ');
        },
        getAllGenres: async function() {
          let allGenres = await axios.get(`https://api.themoviedb.org/3/genre/movie/list?api_key=${this.apiKey}`);
          this.genres = allGenres.data.genres;
          console.log('generi', this.genres);
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