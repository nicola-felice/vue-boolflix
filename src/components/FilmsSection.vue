<template>
  <section>
    
    <h3 v-if="searchText != ''">TV SERIES</h3>
    <div class="container_relative">
      <div class="films_list_wrapper" @scroll="checkArrowVisibility">
        <div @click="scrollLeft" class="arrow_left">
          <font-awesome-icon class="icon" :icon="arrowL" />
        </div>

        <ul>
          <FilmCard v-for="(elm, index) in tvSeriesList" :key="index" :film-data="elm" />
        </ul>

        <div @click="scrollRight" class="arrow_right" :class="(filmsList.length <= 6)? 'hide' : ''">
          <font-awesome-icon class="icon" :icon="arrowR" />
        </div>        
      </div>
    </div>

    <h3 v-if="searchText != ''">FILMS</h3>
    <div class="container_relative">
      <div class="films_list_wrapper" @scroll="checkArrowVisibility">
        <div @click="scrollLeft" class="arrow_left">
          <font-awesome-icon class="icon" :icon="arrowL" />
        </div>

        <ul>
          <FilmCard v-for="(elm, index) in filmsList" :key="index" :film-data="elm" />
        </ul>

        <div @click="scrollRight" class="arrow_right" :class="(filmsList.length <= 6)? 'hide' : ''">
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
    checkArrowVisibility(ev) {
      let element = ev.target;

      // show arrow left
      element.querySelector(".arrow_left").classList.add("show");
      // show arrow right
      element.querySelector(".arrow_right").classList.remove("hide");

      // if you cannot scroll more to the right => hide arrow_right
      const screenWidth = document.querySelector("html").clientWidth;
      if ( (element.scrollLeft + 200) >= (element.scrollWidth - screenWidth - 10) ) {
        element.querySelector(".arrow_right").classList.add("hide");
      }
      // if you cannot scroll more to the left => hide arrow_left
      if ( (element.scrollLeft - 200) <= 0 ) {
        element.querySelector(".arrow_left").classList.remove("show");
      }
    },
    scrollRight(ev) {
      let element = ev.target;
      // fai un while verificando l'id del parent node per prendere la lista di film
      while ( !element.classList.contains('films_list_wrapper') ) {
        element = element.parentNode;
      }
      element.scrollLeft = element.scrollLeft + 1000;

      // show arrow left
      const arrowLeftElm = element.querySelector(".arrow_left");
      arrowLeftElm.classList.add("show");

      // if you cannot scroll more to the right => hide arrow_right
      const screenWidth = document.querySelector("html").clientWidth;
      if ( (element.scrollLeft + 1000) >= (element.scrollWidth - screenWidth - 10) ) {
        element.querySelector(".arrow_right").classList.add("hide");
      }
    },
    scrollLeft(ev) {
      let element = ev.target;
      // fai un while verificando l'id del parent node per prendere la lista di film
      while ( !element.classList.contains('films_list_wrapper') ) {
        element = element.parentNode;
      }
      element.scrollLeft = (element.scrollLeft) - 1000;

      // show arrow right
      element.querySelector(".arrow_right").classList.remove("hide");

      // if you cannot scroll more to the left => hide arrow_left
      if ( (element.scrollLeft - 1000) <= 0 ) {
        element.querySelector(".arrow_left").classList.remove("show");
      }
    },
  }
}
</script>

<style scoped lang="scss">  
  section {
    .container_relative {
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
        height: 35%;
        top: 50%;
        transform: translateY(-50%);
        width: 50px;
        background-color: rgba(0, 0, 0, 0.808);
        cursor: pointer;

        display: flex;
        justify-content: center;
        align-items: center;
        .icon {
          color: rgba(255, 255, 255, 0.815);
          font-size: 2rem;
        }
      }
      .arrow_left {
        left: 0;
        border-radius: 0 1rem 1rem 0;
        display: none;
        &.show {
          display: flex;
        }
      }
      .arrow_right {
        right: 0;
        border-radius: 1rem 0 0 1rem;
        &.hide {
          display: none;
        }
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