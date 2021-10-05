<template>
  <div id="app">
    <Header @searchText="searchText" />

    <main>
      <FilmsSection :searchText="searchTextInput" />
    </main>
  </div>
</template>

<script>
import Header from './components/Header.vue';
import FilmsSection from './components/FilmsSection.vue';

export default {
  name: 'App',
  data() {
    return {
      searchTextInput: "",
    }
  },
  components: {
    Header,
    FilmsSection
  },
  methods: {
    searchText(input) {
      this.searchTextInput = input;
    }
  }
}
</script>

<style lang="scss">

</style>
