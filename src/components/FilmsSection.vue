<template>
  <section>
    <h3>{{sectionTitle}}</h3>
    <div class="container_relative">
      <div ref="films_list_wrapper" class="films_list_wrapper" @scroll="checkArrowVisibility">
        <div @click="scrollLeft" ref="arrow_left" class="arrow_left">
          <font-awesome-icon class="icon" :icon="arrowL" />
        </div>

        <ul>
          <FilmCard v-for="(elm, index) in filmsListData" :key="index" :film-data="elm" />
        </ul>

        <div @click="scrollRight" ref="arrow_right" class="arrow_right">
          <font-awesome-icon class="icon" :icon="arrowR" />
        </div>
      </div>
    </div>
  </section>
</template>

<script>
import FilmCard from './FilmCard.vue';
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';
import { faChevronRight, faChevronLeft } from '@fortawesome/free-solid-svg-icons';

export default {
  name: 'FilmsSection',

  components: {
    FilmCard,
    FontAwesomeIcon
  },

  props: ['filmsListData', 'sectionTitle'],

  data() {
    return {
      // icons
      arrowR: faChevronRight,
      arrowL: faChevronLeft,
    }
  },

  methods: {
    checkArrowVisibility() {
      const filmListWrapper = this.$refs.films_list_wrapper;

      // show arrow left
      this.$refs.arrow_left.classList.add("show");
      // show arrow right
      this.$refs.arrow_right.classList.remove("hide");

      // if you cannot scroll more to the right => hide arrow_right
      const screenWidth = document.querySelector("html").clientWidth;
      if ( (filmListWrapper.scrollLeft + 30) >= (filmListWrapper.scrollWidth - screenWidth - 10) ) {
        this.$refs.arrow_right.classList.add("hide");
      }

      // if you cannot scroll more to the left => hide arrow_left
      if ( (filmListWrapper.scrollLeft - 30) <= 0 ) {
        this.$refs.arrow_left.classList.remove("show");
      }
    },
    scrollRight(ev) {
      let element = ev.target;
      // fai un while verificando l'id del parent node per prendere la lista di film
      while ( !element.classList.contains('films_list_wrapper') ) {
        element = element.parentNode;
      }
      element.scrollLeft = element.scrollLeft + 1200;
    },
    scrollLeft(ev) {
      let element = ev.target;
      // fai un while verificando l'id del parent node per prendere la lista di film
      while ( !element.classList.contains('films_list_wrapper') ) {
        element = element.parentNode;
      }
      element.scrollLeft = (element.scrollLeft) - 1200;
    },
  },

  updated() {
    this.checkArrowVisibility();
  },

  mounted() {
    this.checkArrowVisibility();
  }
}
</script>

<style scoped lang="scss">  
  section {
    background: #141414;
    background: linear-gradient(0deg, #141414 60%, rgba(0,0,0,0) 100%); 
    position: relative;
    z-index: 4;
    padding-top: 5rem;
    .container_relative {
      position: relative;
      padding: 0;
    }
    .films_list_wrapper {
      overflow: auto;
      overflow-y: hidden;
      scroll-behavior: smooth;
      .arrow_left,
      .arrow_right {
        position: absolute;
        z-index: 99;
        height: 3.75rem;
        top: 50%;
        transform: translateY(-50%);
        width: 3.75rem;
        background-color: rgba(0, 0, 0, 0.808);
        cursor: pointer;
        border-radius: 50%;

        display: flex;
        justify-content: center;
        align-items: center;
        .icon {
          color: rgba(255, 255, 255, 0.815);
          font-size: 2.5rem;
        }
      }
      .arrow_left {
        left: 0;
        display: none;
        padding-right: .25rem;
        &.show {
          display: flex;
        }
      }
      .arrow_right {
        right: 0;
        padding-left: .25rem;
        &.hide {
          display: none;
        }
      }
      ul {
        display: flex;
        align-items: center;
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