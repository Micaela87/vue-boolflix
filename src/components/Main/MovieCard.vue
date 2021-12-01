<template>
  <div>
      <ul>
          <li>{{ details.originalTitle }}</li>
          <li>{{ details.title }}</li>
          <li>
              <img :src="details.language === 'it' ? require('@/assets/img/ita.png') : require('@/assets/img/eng.png')" alt="">
              <!-- {{ details.language }} -->
          </li>
            
          <li>{{ details.rating }}</li>
      </ul>
  </div>
</template>

<script>

export default {
  name: 'Main',
  props: {
      details: Object
  },
}
</script>

<style scoped lang="scss">

</style>