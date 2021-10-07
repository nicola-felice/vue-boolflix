<template>
  <li>
    <div id="film_card">
      <div id="front_card">
        <img class="poster" :src="imageSource()" alt="">
      </div>

      <div id="back_card">
        <div>
          <h4>Titolo:</h4>
          <div class="title">{{(filmData.title)? filmData.title : filmData.name}}</div>          
        </div>

        <div>
          <h4>Titolo originale:</h4>
          <div class="title">{{(filmData.original_title)? filmData.original_title : filmData.original_name}}</div>  
        </div>

        <div class="overview">
          <h4>Overview:</h4>
          <p>{{filmData.overview}}</p>
        </div>

        <div class="vote_stars">
          <font-awesome-icon class="star" :class="(isStarGolden(id))? 'golden' : ''" v-for="id in 5" :key="id" :icon="starIcon" />
        </div>

        <div class="country_flag">
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
    }
  }
}
</script>

<style scoped lang="scss">
  li {
    margin: .15rem;
    color: white;
    &:first-child {
      margin-left: 2.5vw;
    }
    #film_card {
      height: 22vw;
      width: 15vw;
      position: relative;
      border-radius: .35rem;
      overflow: hidden;
      #front_card {
        position: absolute;
        z-index: 2;
        top: 0;
        bottom: 0;
      }
      &:hover {
        #front_card {
          z-index: -99;
        }
      }
      #back_card {
        background-color: black;
        height: 100%;
        width: 100%;
        padding: 1rem;
        display: flex;
        flex-direction: column;
        overflow: auto;

        // position: absolute;
        // z-index: 1;
        .vote_stars {
          padding-top: 2rem;
          margin-top: auto;
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
    position: absolute;
    bottom: 0;
    right: 0;
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