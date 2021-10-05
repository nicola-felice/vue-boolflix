<template>
  <section>
    <ul>
      <FilmCard v-for="(elm, index) in filmsList" :key="index" :film-data="elm" />
    </ul>
  </section>
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
    filmsRequest: async function(text) {
      let res = await axios.get('https://api.themoviedb.org/3/search/movie?api_key=ef791ca0153b5b4ddac7daddda0a384a', {
        params: {
          query: text,
        }
      });

      let list = res.data.results;

      await Promise.all(list.map(async (elm) => {
        let resp = await axios.get(`https://api.themoviedb.org/3/movie/${elm.id}?api_key=ef791ca0153b5b4ddac7daddda0a384a`);
        
        const productionCountries = resp.data.production_countries[0];

        if ( productionCountries == undefined ) {
          elm.production_countries = ""; 
        } else {
          elm.production_countries = productionCountries.iso_3166_1;
        }
      }));

      // for await (const elm of list) {
      //   let resp = await axios.get(`https://api.themoviedb.org/3/movie/${elm.id}?api_key=ef791ca0153b5b4ddac7daddda0a384a`);

      //   const productionCountries = resp.data.production_countries[0];

      //   if ( productionCountries == undefined ) {
      //     elm.production_countries = ""; 
      //   } else {
      //     elm.production_countries = productionCountries.iso_3166_1;
      //   }
      // }
      this.filmsList = list;
    }
  },
}
</script>

<style scoped lang="scss">  

</style>