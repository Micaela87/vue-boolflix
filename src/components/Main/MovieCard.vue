<template>
  <div class="card">
      <div class="container">
          <ul class="details">
            <li><strong>Titolo originale: </strong>{{ details.originalTitle }}</li>
            <li><strong>Titolo: </strong>{{ details.title }}</li>
            <li><strong>Lingua: </strong>
                <img v-if="details.language === 'it' || details.language === 'en'" class="language" :src="require('@/assets/img/' + details.language + '.png')" :alt="details.language">
                <span v-else>{{ details.language.toUpperCase() }}</span>               
            </li>
            <li><strong>Cast: </strong>{{ details.cast }}</li> 
            <li><strong>Voto: </strong>
                <span v-if="ratingOutOfMaxRating">{{ ratingOutOfMaxRating }}/{{maxRating}}</span>
                <span v-else>Voto non disponibile</span>
                <div>
                    <font-awesome-icon v-for="n in maxRating" :key="n" :id="n" :icon="['fas', 'star']" :class="n <= ratingOutOfMaxRating ? 'rated' : 'not-rated'"/>
                </div>
            </li>
            <li v-if="details.overview"><strong>Overview: </strong>{{ details.overview }}</li> 
            <li v-else><strong>Overview: </strong>Overview non disponibile</li>        
          </ul>
          <div class="poster">
            <img :src="details.poster" :alt="details.title">              
          </div>
      </div>
  </div>
</template>

<script>

export default {
  name: 'Main',
  props: {
      details: Object
  },
  data() {
      return {
          maxRating: 5,
      }
  },
  computed: {
      ratingOutOfMaxRating() {
          if (this.details.rating) {
              return Math.floor((this.maxRating * this.details.rating) / 10);
          } else {
              return false;
          }
      },
  },
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
    }

    .container {
        position: relative;
        padding: 4rem 1rem;
    }

    .details {
        display: none;
    }

    .details li {
        margin: 0.5rem 0;
    }

    .language {
        width: 2rem;
        height: 1rem;
        line-height: 1.4;
        vertical-align: middle;
    }
    .rated {
        color: yellow;
    }

    .not-rated {
        color: white;
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

    .card:hover .details {
        display: block;
    }

    .card:hover .poster {
        display: none;
    }
</style>