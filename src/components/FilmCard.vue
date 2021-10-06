<template>
  <li class="film_card">
    <div>titolo:<span>{{(filmData.title)? filmData.title : filmData.name}}</span></div>
    <div>titolo originale:<span>{{(filmData.original_title)? filmData.original_title : filmData.original_name}}</span></div>
    <div class="poster_image" :style="`background-image: url(${require('../assets/sad.png')});`">
      <img :src="imageSource()" alt="">
    </div>
    <div class="country_flag">
      paese:
      <country-flag v-if="isFlagVisible()" class="flag" :country='countryFlagCode()' :size='size'/>
      <span v-else>--info non trovata--</span>
    </div>
    <div class="vote_stars">
      voto:
      <span>
        <font-awesome-icon class="star" :class="(isStarGolden(id))? 'golden' : ''" v-for="id in 5" :key="id" :icon="starIcon" />
      </span>
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
        return null;
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
        console.warn(`image not found`);
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
    margin: 2rem 0;

    div {
      display: flex;
      align-items: center;
    }

    .flag {
      display: inline-block;
      margin-left: 0;
    }
  }
  span {
    margin-left: 1rem;
  }

  .poster_image {
    height: 200px;
    width: 150px;
    background-color: #888888;
    background-size: 50%;
    background-repeat: no-repeat;
    background-position: center;

    img {
      width: 100%;
      height: 100%;
      object-fit: cover;
      object-position: center;
    }


  }
  .star {
    color: #8d8c8c;
    &.golden  {
    color: #eece00;
    }
  }
</style>