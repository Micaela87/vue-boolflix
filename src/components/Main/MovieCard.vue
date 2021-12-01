<template>
  <div>
      <ul>
          <li>{{ details.originalTitle }}</li>
          <li>{{ details.title }}</li>
          <li>{{ details.language }}</li>
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