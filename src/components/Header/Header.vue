<template>
  <header>
    <div id="logo">BOOTFLIX</div>
    <form action="">
        <input type="text" placeholder="Search a movie or a TV series" v-model.trim="searchParam">
        <button @click.prevent="$emit('sendParam', searchParam)">Search</button>
        <button @click.prevent="resetSearch">Reset Search</button>
    </form>
  </header>
</template>

<script>
// import axios from 'axios';

export default {
  name: 'Header',
  data() {
      return {
          searchParam: '',
      }
  },
  methods: {
      resetSearch() {
          this.searchParam = '';
          this.$emit('sendParam', this.searchParam);
      }
  },
}
</script>

<style scoped lang="scss">
    header {
        height: 10rem;
        background-color: black;
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding: 0 2rem;
    }

    #logo {
        color: red;
        font-size: 2rem;
        font-weight: bold;
        width: fit-content;
    }

    input {
        width: 40rem;
    }

    ::placeholder {
        font-family: 'Roboto';
    }

    button, input {
        height: 3rem;
        padding: 0 1rem;
    }
</style>