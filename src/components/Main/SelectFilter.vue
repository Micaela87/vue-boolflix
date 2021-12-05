<template>
    <form>
        <label for="genre">{{ labelText }}</label>
        <select :ref="reference" name="genre" id="genre" v-model="selectedGenre" @change="selectionMade">
            <option value="" selected>All</option>
            <option :value="genre.name" v-for="(genre, i) in genre" :key="i">{{ genre.name }}</option>
        </select>
    </form>
</template>

<script>
export default {
  name: 'SelectFilter',
  props: {
      genre: Array,
      labelText: String,
      toBeDisabled: Boolean,
      reference: String
  },
  data() {
      return {
          selectedGenre: '',
          hasBeenSelected: false
      };
  },
  watch: {
      toBeDisabled() {
          if (this.toBeDisabled) {
            this.$refs[this.reference].setAttribute('disabled', true);
          } else {
             this.$refs[this.reference].removeAttribute('disabled'); 
          }
      }
  },
  methods: {
      selectionMade() {
          this.hasBeenSelected = true;
          this.$emit('sendValue', this.selectedGenre, this.hasBeenSelected);
      }
  },
}
</script>

<style scoped lang="scss">
    form {
      padding: 2rem;
    }

    label {
      font-size: 1.6rem;
      margin: 0 1rem;
    }

    option, select {
      font-size: 1.6rem;
      font-family: 'Roboto', sans-serif;
    }
</style>