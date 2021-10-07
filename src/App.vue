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
  @import './assets/style/common.scss';
  @import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@300;400;500;600;700;800&display=swap');

  Header {
    margin-bottom: 5rem;
  }
</style>
