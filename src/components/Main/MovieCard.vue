<template>
  <div>
      <ul>
          <li>{{ details.originalTitle }}</li>
          <li>{{ details.title }}</li>
          <li>{{ details.overview }}</li>
          <li>
              <img :src="details.language === 'it' ? require('@/assets/img/ita.png') : require('@/assets/img/eng.png')" alt="">
              <!-- {{ details.language }} -->
          </li> 
          <li>
              {{ ratingOutOfFive }}
              <div>
                  <font-awesome-icon v-for="n in 5" :key="n" :id="n" :icon="['fas', 'star']" :class="n <= ratingOutOfFive ? 'rated' : 'not-rated'"/>
              </div>
          </li>
          <li>
            <img :src="details.poster" :alt="details.title">
          </li>   
      </ul>
  </div>
</template>

<script>

export default {
  name: 'Main',
  props: {
      details: Object
  },
  computed: {
      ratingOutOfFive() {
          if (this.details.rating) {
              return Math.floor((5 * this.details.rating) / 10);
          } else {
              return 'Voto non disponibile';
          }
      }
  }
}
</script>

<style scoped lang="scss">
    .rated {
        color: blue;
    }

    .not-rated {
        color: yellow;
    }
</style>