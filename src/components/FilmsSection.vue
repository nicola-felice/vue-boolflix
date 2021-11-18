<template>
  <section>
    <h3>{{sectionTitle}}</h3>
    <div class="container_relative" @mouseover="onHover" @mouseleave="hideArrows">
      <div ref="films_list_wrapper" class="films_list_wrapper" @scroll="isScrollEnded">
        <div @click="scrollLeft" ref="arrow_left" class="arrow_left">
          <font-awesome-icon class="icon" :icon="arrowL" />
        </div>

        <ul>
          <film-card @previewData="showPreview" v-for="(elm, index) in filmsListData" :key="index" :film-data="elm" />
        </ul>

        <div @click="scrollRight" ref="arrow_right" class="arrow_right hide">
          <font-awesome-icon class="icon" :icon="arrowR" />
        </div>
      </div>
    </div>
    <!-- hover card component -->
    <preview-card :position="cardHoverPosition" :film-data="cardHoverData" />
  </section>
</template>

<script>
import FilmCard from './FilmCard.vue';
import PreviewCard from './PreviewCard.vue';
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';
import { faChevronRight, faChevronLeft } from '@fortawesome/free-solid-svg-icons';

export default {
  name: 'FilmsSection',

  components: {
    FilmCard,
    FontAwesomeIcon,
    PreviewCard
  },

  props: ['filmsListData', 'sectionTitle'],

  data() {
    return {
      // icons
      arrowR: faChevronRight,
      arrowL: faChevronLeft,

      timerScroll: null,

      // data film hovered
      cardHoverData: null,
      cardHoverPosition: null,
    }
  },

  methods: {
    showPreview(coordinates, filmData){
      this.cardHoverData = filmData;
      this.cardHoverPosition = coordinates;
    },
    checkArrowVisibility() {
      const filmListWrapper = this.$refs.films_list_wrapper;
      const leftArrowDomElm = this.$refs.arrow_left;
      const rightArrowDomElm = this.$refs.arrow_right;

      leftArrowDomElm.classList.add("show");
      rightArrowDomElm.classList.remove("hide");

      const screenWidth = document.querySelector("html").clientWidth;
      const canScrollToRight = (filmListWrapper.scrollLeft + 30) < (filmListWrapper.scrollWidth - screenWidth - 10);
      if ( !canScrollToRight ) {
        rightArrowDomElm.classList.add("hide");
      }

      const canScrollToLeft = (filmListWrapper.scrollLeft - 30) <= 0;
      if ( canScrollToLeft ) {
        leftArrowDomElm.classList.remove("show");
      }
    },
    isScrollEnded() {
      const isTimeoutSet = this.timerScroll !== null;
      if(isTimeoutSet) {
        clearTimeout(this.timerScroll);        
      }
      this.timerScroll = setTimeout( () => {
        this.hideArrows();
      }, 150);
    },
    onHover() {
      this.checkArrowVisibility();
    },
    hideArrows() {
      this.$refs.arrow_left.classList.remove("show");
      this.$refs.arrow_right.classList.add("hide");
    },
    scrollRight() {
      const filmListWrapper = this.$refs.films_list_wrapper;
      filmListWrapper.scrollLeft += 1200;
    },
    scrollLeft() {
      const filmListWrapper = this.$refs.films_list_wrapper;
      filmListWrapper.scrollLeft -= 1200;
    },
  },
}
</script>

<style scoped lang="scss">  
  section {
    padding-top: 1.25rem;

    .container_relative {
      position: relative;
      background: #141414;
      z-index: 4;
      padding: 0;

      .hover_card {
        &:hover {
          background-color: red;
        }
      }
    }
    .films_list_wrapper {
      overflow-x: auto;
      overflow-y: hidden;
      scroll-behavior: smooth;

      padding: 1rem 0;
      .arrow_left,
      .arrow_right {
        position: absolute;
        z-index: 99;
        top: 2rem;
        bottom: 2rem;
        width: 3.75rem;
        background-color: rgba(0, 0, 0, 0.418);
        cursor: pointer;

        display: flex;
        justify-content: center;
        align-items: center;

        transition: all 300ms ease-in-out;
        .icon {
          color: rgb(255, 255, 255);
          font-size: 1.5rem;
        }
        &:hover {
          background-color: rgba(0, 0, 0, 0.6);
          .icon {
            transform: scale(1.2);
          }
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