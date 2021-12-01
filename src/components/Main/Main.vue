<template>
  <main>
      <div class="results" v-if="this.movies.length">
        <MovieCard
        v-for="(movie, i) in movies" :key="i"
        :details="movie"/>
      </div>
      <div class="results" v-if="!this.movies.length && this.active">
        <h2>La ricerca non ha prodotto risultati</h2>
      </div>
      <div class="results" v-if="!this.active">
        <h2>Usa la barra di ricerca in alto per trovare il tuo film preferito</h2>
      </div>
  </main>
</template>

<script>
import MovieCard from "./MovieCard.vue";

export default {
    name: "Main",
    components: {
        MovieCard
    },
      props: {
          movies: Array,
          active: Boolean
    }
}
</script>

<style scoped lang="scss">
    main {
        background-color: grey;
        height: calc(100vh - 10rem);
    }

    .results {
        display: flex;
        flex-wrap: wrap;
        justify-content: space-between;
        align-items: center;
        height: 100%;
        width: 100%;
    }

    h2 {
        width: 100%;
        text-align: center;
        font-size: 2rem;
    }
</style>