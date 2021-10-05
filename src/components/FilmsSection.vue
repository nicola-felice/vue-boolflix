<template>
  <div>
    <ul>
      <FilmCard v-for="(elm, index) in filmsList" :key="index" :filmData="elm" />
    </ul>
  </div>
</template>

<script>
import axios from 'axios';
import FilmCard from './FilmCard.vue';

export default {
  name: 'FilmsSection',
  components: {
    FilmCard,
  },
  props: ['searchText'],
  data() {
    return {
      filmsList: [],
    }
  },
  watch: {
    searchText: function(val) {
      // after the user makes a search
      if ( val == "" ) {
        // if the input is "", empty the list 
        this.filmsList = [];
      } else {
        // else make a request for the data
        this.filmsRequest(val);
      }
    }
  },
  methods: {
    filmsRequest(text) {
      axios.get('https://api.themoviedb.org/3/search/movie?api_key=ef791ca0153b5b4ddac7daddda0a384a', {
        params: {
          query: text,
        }
      }).then( res => {
        this.filmsList = res.data.results;
      });
    }
  },
}
</script>

<style scoped lang="scss">  

</style>