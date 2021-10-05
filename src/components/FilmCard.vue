<template>
  <li class="filmCard">
    <div>titolo:<span>{{filmData.title}}</span></div>
    <div>titolo originale:<span>{{filmData.original_title}}</span></div>
    <div>
      paese:
      <country-flag v-if="(filmData.production_countries != '')" class="flag" :country='filmData.production_countries' :size='size'/>
      <span v-else>--info non disponibile--</span>
    </div>
    <div>voto:<span>{{filmData.vote_average}}</span></div>
  </li>
</template>

<script>
import CountryFlag from 'vue-country-flag';

export default {
  name: 'FilmCard',
  components: {
    CountryFlag,
  },
  data() {
    return{
      sizeFlags: 'medium',
    }
  },
  props:['filmData', 'size'],
}
</script>

<style scoped lang="scss">
  li {
    margin: 1rem 0;

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
</style>