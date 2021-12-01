<template>
  <main>
      <MovieCard
      v-for="(movie, i) in movies" :key="i"
      :details="movie"/>
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
          movies: Array
    },
}
</script>

<style scoped lang="scss">
    main {
        display: flex;
        flex-wrap: wrap;
        justify-content: space-between;
        align-items: center;
        background-color: grey;
    }
</style>