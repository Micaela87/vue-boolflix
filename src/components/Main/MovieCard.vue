<template>
  <div class="card">
      <ul>
          <li><strong>Titolo originale: </strong>{{ details.originalTitle }}</li>
          <li><strong>Titolo: </strong>{{ details.title }}</li>
          <li><strong>Overview: </strong>{{ details.overview }}</li>
          <li><strong>Lingua: </strong>
              <img class="language" :src="details.language === 'it' ? require('@/assets/img/ita.png') : require('@/assets/img/eng.png')" alt="">
              <!-- {{ details.language }} -->
          </li> 
          <li><strong>Voti: </strong>
              {{ ratingOutOfFive }}
              <div>
                  <font-awesome-icon v-for="n in 5" :key="n" :id="n" :icon="['fas', 'star']" :class="n <= ratingOutOfFive ? 'rated' : 'not-rated'"/>
              </div>
          </li>
          <li class="poster">
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
    .card {
        width: 25%;
        min-height: 60rem;
        margin: 1rem;
        background-color: black;
        border: 1px solid white;
    }

    ul {
        list-style-type: none;
        font-size: 1.5rem;
        line-height: 1.4;
        color: white;
        position: relative;
        padding: 4rem 1rem;
    }

    // li {
    //     margin: 0.5rem 0;
    // }

    .language {
        width: 2rem;
        height: 1rem;
        line-height: 1.4;
        vertical-align: middle;
    }
    .rated {
        color: blue;
    }

    .not-rated {
        color: yellow;
    }

    .poster {
        width: 100%;
        height: 59.8rem;
        position: absolute;
        top: 0;
        left: 0;
        z-index: 1;
        overflow: hidden;
    }

    .poster img {
        width: 100%;
        height: 100%;
        object-fit: cover;
    }
</style>