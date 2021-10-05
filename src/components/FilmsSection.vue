<template>
  <div>
    <ul>
      <li v-for="(elm, index) in filmsList" :key="index" class="filmCard">
        <div>titolo:<span>{{elm.title}}</span></div>
        <div>titolo originale:<span>{{elm.original_title}}</span></div>
        <div>lingua originale:<span>{{elm.original_language}}</span></div>
        <div>voto:<span>{{elm.vote_average}}</span></div>
      </li>
    </ul>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'FilmsSection',
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

  li {
    margin: 1rem 0;
  }
  span {
    margin-left: 1rem;
  }
</style>