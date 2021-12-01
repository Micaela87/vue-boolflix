<template>
  <div>
      <MovieCard
      v-for="(movie, i) in movies" :key="i"
      :details="movie"/>
  </div>
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

</style>