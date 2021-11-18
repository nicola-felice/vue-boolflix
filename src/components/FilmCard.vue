<template>
  <li @mouseenter="onHover" @mouseleave="onMouseLeave" 
    id="film_card" ref="film_card" class="film_card">
    <div class="poster_wrapper">
      <img class="poster" :src="imageSource" alt="">
    </div>
  </li>
</template>

<script>

export default {
  name: 'FilmCard', 

  props:['filmData'],

  data() {
    return {
      hoverTimeout: null,
    }
  },

  methods: {
    onHover(e) {
      const COORDINATES = e.target.getBoundingClientRect();

      // emit after 1 second of hovering
      this.hoverTimeout = setTimeout(() => {
        this.$emit('previewData', COORDINATES, this.filmData);
      }, 500);
    },
    onMouseLeave() {
      // if mouse leave clear timeout 
      clearTimeout(this.hoverTimeout);
    }
  },

  computed: {
    imageSource() {
      if ( this.filmData.backdrop_path == null ) {
        return "#";
      } else {
        return `https://image.tmdb.org/t/p/w500/${this.filmData.backdrop_path}`;
      }
    }
  }
}
</script>

<style scoped lang="scss">

#film_card {
  user-select: none;
}

#film_card:first-child {
  margin: 1rem 0;
  margin-left: 3rem;
}
.poster_wrapper {
  width: 15vw;
  padding: 0 .17rem;
  position: relative;
}
.poster {
  cursor: pointer;
  width: 100%;
  object-fit: cover;
  border-radius: .25rem;
}

::marker {
  color: transparent;
}
</style>