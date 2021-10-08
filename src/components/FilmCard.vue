<template>
  <li>
    <div id="film_card" ref="film_card">
      <div id="front_card">
        <img class="poster" :src="imageSource()" alt="">
      </div>

      <div id="back_card">
        <div>
          <h4>Title:</h4>
          <div class="title">{{(filmData.title)? filmData.title : filmData.name}}</div>          
        </div>

        <div>
          <h4 v-if="filmData.cast.length > 0">Actors:</h4>
          <div id="actors" class="title">
            <span v-for="(elm, index) in filmData.cast" :key="index">{{elm.name}}<span v-if="index != filmData.cast.length - 1"> - </span></span> 
          </div>  
        </div>

        <div class="overview">
          <h4>Overview:</h4>
          <p>{{filmData.overview}}</p>
        </div>

        <div class="flag_vote_wrapper">
          <font-awesome-icon class="star" :class="(isStarGolden(id))? 'golden' : ''" v-for="id in 5" :key="id" :icon="starIcon" />
          <country-flag class="flag" :country='countryFlagCode()' :size='size'/>
        </div>  
      </div>      
    </div>


  </li>
</template>

<script>
import CountryFlag from 'vue-country-flag';
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';
import { faStar } from '@fortawesome/free-solid-svg-icons';

export default {
  name: 'FilmCard', 
  components: {
    CountryFlag,
    FontAwesomeIcon
  },
  data() {
    return{
      sizeFlags: 'medium',
      starIcon: faStar,
    }
  },
  props:['filmData', 'size'],
  methods: {
    isFlagVisible() {
      if ( this.filmData.production_countries != '' && this.filmData.production_countries != undefined ) {
        return true;

      } else if ( this.filmData.origin_country != undefined && this.filmData.origin_country.length > 0 ) {
        return true;
      }
      return false;
    },
    countryFlagCode() {
      // if flag is not visible => return null
      if ( !this.isFlagVisible() ) {
        return "";
      }
      // else return the correct string to display the flag
      if ( this.filmData.origin_country != undefined && this.filmData.origin_country.length > 0 ) {
        return this.filmData.origin_country[0];

      } else {
        return this.filmData.production_countries;
      }
    },
    imageSource() {
      if ( this.filmData.poster_path == null ) {
        return "#";
      } else {
        return `https://image.tmdb.org/t/p/w500/${this.filmData.poster_path}`;
      }
    },
    isStarGolden(id) {
      // return true if id is less or = to vote average (in a scale 1 to 5)
      const stars = Math.round(this.filmData.vote_average / 2);
      if ( id < stars ) {
        return true;
      }
      return false;
    },
  }
}
</script>

<style scoped lang="scss">
  li {
    margin: 2.5rem .25rem;
    color: white;
    position: relative;
    height: 22.5rem;
    width: 15rem;
    flex-shrink: 0;
    &:last-child {
      #film_card:hover {
        right: 0;
        left: 0;
        transform: translate(-50%, -50%);
      }
    }
    &:first-child {
      margin-left: 2.5vw;
      #film_card:hover {
        left: 0;
        transform: translate(0,-50%);
      }
    }
    #film_card {
      border-radius: .5rem;
      overflow: hidden;
      height: 100%;
      #front_card {
        height: 100%;
      }
      #back_card {
        background-color: #141414;
        padding: 1rem;
        padding-bottom: 0;
        display: none;
        flex-direction: column;
        .flag_vote_wrapper {
          margin-bottom: .75rem;
          margin-top: auto;
          display: flex;
          align-items: center;
        }
      }
    }
    // on card hover styles
    #film_card:hover {
      position: absolute;
      width: 25rem;
      height: 120%;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      z-index: 3;
      -webkit-box-shadow: 0px 0px 6px 6px rgba(0,0,0,0.4); 
      box-shadow: 0px 0px 6px 6px rgba(0,0,0,0.4);
      #front_card {
        height: 30%;
        width: 100%;
      }
      #back_card {
        display: flex;
        height: 70%;
        p {
          display: -webkit-box;
          -webkit-line-clamp: 4;
          -webkit-box-orient: vertical;  
          overflow: hidden;
        }
      }
    }
  }

  img.poster {
    width: 100%;
    height: 100%;
    object-fit: cover;
    object-position: center;
  }
  .flag {
    display: inline-block;
    margin-left: 0;
  }
  h4 {
    color: rgb(161, 161, 161);
    font-size: .9rem;
    font-weight: 400;
    margin: .3rem 0 .25rem 0;
  }
  .title {
    font-weight: 500;
    margin-bottom: .5rem;
  }
  .star {
    color: #8d8c8c;
    &.golden  {
    color: #eece00;
    }
  }
  
  ::marker {
    color: transparent;
  }
</style>