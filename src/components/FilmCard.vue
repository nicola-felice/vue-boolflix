<template>
  <li @mouseenter="onHover" @mouseleave="onMouseLeave" @click="onClick"
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
    },
    onClick() {
      this.$emit('fullPreviewData', this.filmData);
    }
  },

  computed: {
    imageSource() {
      if ( this.filmData.backdrop_path == null ) {
        return "#";
      } else {
        return `https://image.tmdb.org/t/p/w300/${this.filmData.backdrop_path}`;
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
  @media screen and (max-width: 700px) {
    & {
      margin-left: 1.5rem;
    }
  }
}
.poster_wrapper {
  width: 15vw;
  padding: 0 .17rem;
  position: relative;
  @media screen and (max-width: 850px) {
    & {
      width: 20vw;
    }
  }
  @media screen and (max-width: 700px) {
    & {
      width: 25vw;
    }
  }
  @media screen and (max-width: 550px) {
    & {
      width: 35vw;
    }
  }
  @media screen and (max-width: 450px) {
    & {
      width: 45vw;
    }
  }
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