<template>
  <section>
    
    <h3 v-if="searchText != ''">TV SERIES</h3>
    <div class="container">
      <div class="films_list_wrapper">
        <div v-if="searchText != ''" @click="scrollLeft" class="arrow_left">
          <font-awesome-icon class="icon" :icon="arrowL" />
        </div>

        <ul>
          <FilmCard v-for="(elm, index) in tvSeriesList" :key="index" :film-data="elm" />
        </ul>

        <div v-if="searchText != ''" @click="scrollRight" class="arrow_right">
          <font-awesome-icon class="icon" :icon="arrowR" />
        </div>        
      </div>
    </div>

    <h3 v-if="searchText != ''">FILMS</h3>
    <div class="container">
      <div class="films_list_wrapper">
        <div v-if="searchText != ''" @click="scrollLeft" class="arrow_left">
          <font-awesome-icon class="icon" :icon="arrowL" />
        </div>

        <ul>
          <FilmCard v-for="(elm, index) in filmsList" :key="index" :film-data="elm" />
        </ul>

        <div v-if="searchText != ''" @click="scrollRight" class="arrow_right">
          <font-awesome-icon class="icon" :icon="arrowR" />
        </div>
      </div>
    </div>
  </section>
</template>

<script>
import axios from 'axios';
import FilmCard from './FilmCard.vue';
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';
import { faChevronRight, faChevronLeft } from '@fortawesome/free-solid-svg-icons';

export default {
  name: 'FilmsSection',
  components: {
    FilmCard,
    FontAwesomeIcon
  },
  props: ['searchText'],
  data() {
    return {
      filmsList: [],
      tvSeriesList: [],
      // icons
      arrowR: faChevronRight,
      arrowL: faChevronLeft,
    }
  },
  watch: {
    searchText: function(val) {
      // after the user makes a search
      if ( val == "" ) {
        // if the input is "", empty the list 
        this.filmsList = [];
        this.tvSeriesList = [];
      } else {
        // else make a request for the data
        this.filmsRequest(val);
        this.tvSeriesRequest(val);
      }
    }
  },
  methods: {
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
      this.filmsList = list;
    },
    tvSeriesRequest: async function(text) {
      // with the same input search also for tv series
      let res = await axios.get('https://api.themoviedb.org/3/search/tv?api_key=ef791ca0153b5b4ddac7daddda0a384a', {
        params: {
          query: text,
        }
      });
      this.tvSeriesList = res.data.results;
    },
    scrollRight(ev) {
      // console.log(ev.target.id);
      let element = ev.target;
      // fai un while verificando l'id del parent node per prendere la lista di film
      while ( !element.classList.contains('films_list_wrapper') ) {
        element = element.parentNode;
      }
      element.scrollLeft = (element.scrollLeft) + 1000;
    },
    scrollLeft(ev) {
      // console.log(ev.target.id);
      let element = ev.target;
      // fai un while verificando l'id del parent node per prendere la lista di film
      while ( !element.classList.contains('films_list_wrapper') ) {
        element = element.parentNode;
      }
      element.scrollLeft = (element.scrollLeft) - 1000;
    }
  },
}
</script>

<style scoped lang="scss">  
  section {
    .container {
      position: relative;
      padding: 0;
    }
    .films_list_wrapper {
      overflow: auto;
      margin: .75rem 0 4rem 0;
      overflow-y: hidden;
      scroll-behavior: smooth;
      .arrow_left,
      .arrow_right {
        position: absolute;
        z-index: 9999;
        top: 0;
        bottom: 0;
        width: 70px;
        background-color: rgba(0, 0, 0, 0.699);
        cursor: pointer;

        display: flex;
        justify-content: center;
        align-items: center;
        .icon {
          color: rgba(255, 255, 255, 0.815);
          font-size: 3rem;
        }
      }
      .arrow_left {
        left: 0;
      }
      .arrow_right {
        right: 0;
      }
      ul {
        display: flex;
        align-items: center ;
      }
    }
  }

  h3 {
    color: white;
    margin-left: 3rem;
  }

  /* Hide scrollbar for Chrome, Safari and Opera */
  .films_list_wrapper::-webkit-scrollbar {
    display: none;
  }
  /* Hide scrollbar for IE, Edge and Firefox */
  .films_list_wrapper {
    -ms-overflow-style: none;  /* IE and Edge */
    scrollbar-width: none;  /* Firefox */
  } 
</style>