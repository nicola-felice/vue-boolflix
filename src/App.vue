<template>
  <div id="app">
    <Header @searchText="search" />

    <main>
      <Hero />

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
import Hero from './components/Hero.vue';

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
    FilmsSection,
    Hero,
  },
  methods: {
    async search(input) {
      await this.filmsRequest(input);
      await this.tvSeriesRequest(input);
      window.scrollTo(0, 600);
    },
    async filmsRequest(text) {
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

      // get cast
      await Promise.all(list.map(async (elm) => {
        let resp = await axios.get(`https://api.themoviedb.org/3/movie/${elm.id}/credits?api_key=ef791ca0153b5b4ddac7daddda0a384a&language=en-US`);

        elm.cast = resp.data.cast;
        // take only actors
        elm.cast = elm.cast.filter( (elm, index) => elm.known_for_department == 'Acting' && index < 5 );
      }));

      // remove those with no image
      list = list.filter( elm => elm.poster_path != null );

      this.filmsList = list;
    },
    async tvSeriesRequest(text) {
      // with the same input search also for tv series
      let res = await axios.get('https://api.themoviedb.org/3/search/tv?api_key=ef791ca0153b5b4ddac7daddda0a384a', {
        params: {
          query: text,
        }
      });
      let list = res.data.results;

      // get cast
      await Promise.all(list.map(async (elm) => {
        let resp = await axios.get(`https://api.themoviedb.org/3/tv/${elm.id}/credits?api_key=ef791ca0153b5b4ddac7daddda0a384a&language=en-US`);

        elm.cast = resp.data.cast;
        // take only the actors
        elm.cast = elm.cast.filter( (elm, index) => elm.known_for_department == 'Acting' && index < 5 );
      }));

      // remove those with no image
      list = list.filter( elm => elm.poster_path != null );

      this.tvSeriesList = list;
    },
    async suggestedSeriesRequets() {
      let res = await axios.get('https://api.themoviedb.org/3/trending/tv/week?api_key=ef791ca0153b5b4ddac7daddda0a384a');
      let list = res.data.results;

      // get first 5 actors
      await Promise.all(list.map(async (elm) => {
        let resp = await axios.get(`https://api.themoviedb.org/3/tv/${elm.id}/credits?api_key=ef791ca0153b5b4ddac7daddda0a384a&language=en-US`);

        elm.cast = resp.data.cast;
        // take only the actors
        elm.cast = elm.cast.filter( (elm, index) => elm.known_for_department == 'Acting' && index < 5 );
      }));

      // remove those with no image
      list = list.filter( elm => elm.poster_path != null );
      this.trendingTvSeries = list;
    },
    async suggestedMoviesRequest() {
      let res = await axios.get('https://api.themoviedb.org/3/trending/movie/week?api_key=ef791ca0153b5b4ddac7daddda0a384a');
      let list = res.data.results;

      // get movie country of production
      await Promise.all(list.map(async (elm) => {
        let resp = await axios.get(`https://api.themoviedb.org/3/movie/${elm.id}?api_key=ef791ca0153b5b4ddac7daddda0a384a`);

        const productionCountries = resp.data.production_countries[0];

        if ( productionCountries == undefined ) {
          elm.production_countries = ""; 
        } else {
          elm.production_countries = productionCountries.iso_3166_1;
        }
      }));

      // get cast
      await Promise.all(list.map(async (elm) => {
        let resp = await axios.get(`https://api.themoviedb.org/3/movie/${elm.id}/credits?api_key=ef791ca0153b5b4ddac7daddda0a384a&language=en-US`);

        elm.cast = resp.data.cast;
        // take only actors
        elm.cast = elm.cast.filter( (elm, index) => elm.known_for_department == 'Acting' && index < 5 );
      }));

      // remove those with no image
      list = list.filter( elm => elm.poster_path != null );

      this.trendingMovies = list;
    }
  },
  created() {
    this.suggestedSeriesRequets();
    this.suggestedMoviesRequest();
  },
}
</script>

<style lang="scss">
  @import './assets/style/common.scss';
  @import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@300;400;500;600;700;800&display=swap');

</style>
