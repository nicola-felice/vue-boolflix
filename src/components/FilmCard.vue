<template>
  <li class="filmCard">
    <div>titolo:<span>{{filmData.title}}</span></div>
    <div>titolo originale:<span>{{filmData.original_title}}</span></div>
    <div>lingua originale:<span>{{filmData.original_language}}</span></div>
    <div>voto:<span>{{filmData.vote_average}}</span></div>
  </li>
</template>

<script>
export default {
  name: 'FilmCard',
  props:['filmData'],
}
</script>

<style scoped lang="scss">
  li {
    margin: 1rem 0;
  }
  span {
    margin-left: 1rem;
  }
</style>