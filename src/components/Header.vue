<template>
  <header>
    <input v-model="searchFilmInput" type="text" placeholder="cerca titolo film">
    <button @click="$emit('searchText', searchFilmInput)" id="search_film_btn">cerca</button>
  </header>
</template>

<script>
export default {
  name: 'Header',
  data() {
    return {
      searchFilmInput: "",
    }
  }
}
</script>

<style scoped lang="scss">

</style>