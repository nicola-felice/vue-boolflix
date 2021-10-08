<template>
  <div id="app">
    <Header @searchText="search" />

    <main>
      <FilmsSection v-if="tvSeriesList.length > 0" :section-title="'YOUR TV SERIES'" :films-list-data="tvSeriesList" />

      <FilmsSection v-if="filmsList.length > 0" :section-title="'TOUR FILMS'" :films-list-data="filmsList" />

      <!-- trending films/series -->
      <FilmsSection :section-title="'TRENDING TV SERIES'" :films-list-data="trendingTvSeries" />

      <FilmsSection :section-title="'TRENDING FILMS'" :films-list-data="trendingMovies" />
    </main>
  </div>
</template>

<script>
import axios from 'axios';
import Header from './components/Header.vue';
import FilmsSection from './components/FilmsSection.vue';

export default {
  name: 'App',
  data() {
    return {
      // film lists
      filmsList: [],
      tvSeriesList: [],
      trendingMovies: [],
      trendingTvSeries: [],
    }
  },
  components: {
    Header,
    FilmsSection
  },
  methods: {
    search(input) {
      this.filmsRequest(input);
      this.tvSeriesRequest(input);
    },
    filmsRequest: async function(text) {
      // get the films list
      let res = await axios.get('https://api.themoviedb.org/3/search/movie?api_key=ef791ca0153b5b4ddac7daddda0a384a', {
        params: {
          query: text,
        }
      });
      let list = res.data.results;

      // when you have the films list
      // request for the production country for each film
      // then add the property to list
      await Promise.all(list.map(async (elm) => {
        let resp = await axios.get(`https://api.themoviedb.org/3/movie/${elm.id}?api_key=ef791ca0153b5b4ddac7daddda0a384a`);

        const productionCountries = resp.data.production_countries[0];

        if ( productionCountries == undefined ) {
          elm.production_countries = ""; 
        } else {
          elm.production_countries = productionCountries.iso_3166_1;
        }
      }));

      // remove those with no image
      list = list.filter( elm => elm.poster_path != null );

      this.filmsList = list;
    },
    tvSeriesRequest: async function(text) {
      // with the same input search also for tv series
      let res = await axios.get('https://api.themoviedb.org/3/search/tv?api_key=ef791ca0153b5b4ddac7daddda0a384a', {
        params: {
          query: text,
        }
      });

      let list = res.data.results;
      // remove those with no image
      list = list.filter( elm => elm.poster_path != null );

      this.tvSeriesList = list;
    },
    suggestedSeriesRequets: async function() {
      let res = await axios.get('https://api.themoviedb.org/3/trending/tv/week?api_key=ef791ca0153b5b4ddac7daddda0a384a');
      let list = res.data.results;
      // remove those with no image
      list = list.filter( elm => elm.poster_path != null );
      this.trendingTvSeries = list;
    },
    suggestedMoviesRequest: async function() {
      let res = await axios.get('https://api.themoviedb.org/3/trending/movie/week?api_key=ef791ca0153b5b4ddac7daddda0a384a');
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

      // remove those with no image
      list = list.filter( elm => elm.poster_path != null );

      this.trendingMovies = list;
    }
  },
  mounted() {
    this.suggestedSeriesRequets();
    this.suggestedMoviesRequest();
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
