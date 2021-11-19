<template>
  <div id="preview" ref="preview" @mouseleave="onMouseLeave" 
      @mouseenter="setImageSrc(filmData.backdrop_path), videoRequest(filmData.id, filmData.media_type)">
    <div class="img_wrapper">
      <img ref="poster" src="#" alt="" @load="expandPreview">
      <div class="video_container" ref="video_container">
        <youtube :video-id="videoId" ref="yt" id="yt"
          :player-vars="playerVars" tabindex="-1" @error="onVideoError" style="height: 100%;">
        </youtube>        
      </div>
    </div>
    <div class="info_wrapper">
      <div class="btn_wrapper">
        <button class="controls">
          <font-awesome-icon class="icon" :icon="playIcon" />
        </button>
        <button class="controls">
          <font-awesome-icon class="icon" :icon="plusIcon" />
        </button>
        <button class="controls">
          <font-awesome-icon class="icon" :icon="likeIcon" />
        </button>
        <button class="controls">
          <font-awesome-icon class="icon" :icon="dislikeIcon" />
        </button>
        <button class="controls">
          <font-awesome-icon class="icon" :icon="chevrondown" />
        </button> 
      </div>
      <div class="text_wrapper">
        <span class="compatibility">Novità</span>
        <span class="age_restriction">{{adultContent ? '18+' : 'T'}}</span>
        <span class="duration">{{duration}}</span>
        <span class="video_quality">HD</span>
      </div>
      <div class="genres_wrapper">
        <span v-for="(elm, id) in genresList" :key="id" class="genre">{{elm}}</span>
      </div>
    </div>
  </div>
</template>

<script>
import { faPlay, faPlus, faChevronDown } from '@fortawesome/free-solid-svg-icons';
import { faThumbsUp, faThumbsDown } from '@fortawesome/free-regular-svg-icons';
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';

import axios from 'axios';

export default {
  name: 'PreviewCard',

  props: ['filmData', 'position'],

  components: {
    FontAwesomeIcon
  },

  data() {
    return {
      // icons
      playIcon: faPlay,
      plusIcon: faPlus,
      likeIcon: faThumbsUp,
      dislikeIcon: faThumbsDown,
      chevrondown: faChevronDown,

      // film data
      imgUrl: null,
      duration: null,
      adultContent: false,
      genresList: [],

      // video player
      videoId: null,
      playerVars: {
        autoplay: 0,
        mute: 1,
        controls: 0,
      },

      // timeouts
      timeouts: [],
    }
  },

  methods: {
    displayVideo() {
      this.$refs.yt.player.playVideo();

      this.$refs.video_container.style.opacity = '1';

      this.timeouts.push(setTimeout( () => {
        this.$refs.poster.style.opacity = '0';
      }, 2000));
    },
    onVideoError() {
      this.timeouts.forEach( elm => {
        clearTimeout(elm);
      });
      console.error('video not found or unable to play');
    },
    onMouseLeave() {
      this.$refs.preview.classList.remove('expanded');
      this.$refs.preview.style.left = `-100vw`;

      this.$refs.poster.src = '#';

      this.$refs.yt.player.stopVideo();

      // reset styles and timeouts
      this.$refs.video_container.style.opacity = '0';
      this.$refs.poster.style.opacity = '1';
      this.timeouts.forEach( elm => {
        clearTimeout(elm);
      });
    },
    expandPreview() {
      this.$refs.preview.classList.add('expanded');     
    }, 
    setImageSrc(imgFileName) {
      const imgUrl = `https://image.tmdb.org/t/p/original${imgFileName}`;
      this.$refs.poster.src = imgUrl;
    },
    async getFilmDetails(id, type) {
      const res = await axios.get(`https://api.themoviedb.org/3/${type}/${id}?api_key=ef791ca0153b5b4ddac7daddda0a384a&language=en-US`);
      return res.data;
    },
    async getGenresList(type) {
      const res = await axios.get(`https://api.themoviedb.org/3/genre/${type}/list?api_key=ef791ca0153b5b4ddac7daddda0a384a&language=en-US`);
      return res.data.genres;
    },
    async videoRequest(id, type) {
      this.videoId = null;
      let res = await axios.get(`https://api.themoviedb.org/3/${type}/${id}/videos?api_key=ef791ca0153b5b4ddac7daddda0a384a`);

      res.data.results.forEach( elm => {
        // find a trailer 
        if ( elm.type.includes("Trailer") && this.videoId == null ) {
          this.videoId = elm.key;
          return;
        }
      });

      if ( this.videoId == null ) {
        return;
      }

      this.timeouts.push(setTimeout(() => {
        this.displayVideo();
      }, 3000));
    }
  },

  watch: {
    async filmData(filmData) {
      this.setImageSrc(filmData.backdrop_path);
      // get duration
      const mediaType = filmData.media_type;
      const filmDetails = await this.getFilmDetails(filmData.id, mediaType);

      if (mediaType == 'tv') {
        this.duration = `${filmDetails.number_of_seasons} season`;
        filmDetails.number_of_seasons > 1 ? this.duration += 's' : null;

      } else if (mediaType == 'movie') {
        const hours = Math.floor(filmDetails.runtime / 60);
        let minutes = filmDetails.runtime;
        while ( minutes > 60 ) {
          minutes -= 60;
        }
        this.duration = `${hours}h ${minutes}min`;
      }

      // get if adult content
      this.adultContent = filmDetails.adult;

      // get genres list
      const allGenresList = await this.getGenresList(mediaType);
      this.genresList = [];
      filmData.genre_ids.forEach( myGenreId => {
        allGenresList.forEach( genreObj => {
          if ( myGenreId == genreObj.id ) {
            this.genresList.push(genreObj.name);
          }
        });
      });
    },
    position(hoveredCard) {
      // considering window overflowing
      const isFilmCardOverflowing = hoveredCard.left < 0 || hoveredCard.left >= window.innerWidth - hoveredCard.width;
      const previewOverflowsLeft = hoveredCard.left < 100;
      const previewOverflowsRight = hoveredCard.left >= window.innerWidth - hoveredCard.width - 100;
      const previewDomElm = this.$refs.preview;

      if ( isFilmCardOverflowing ) {
        return null;

      } else if ( previewOverflowsLeft ) {
        previewDomElm.style.left = `${hoveredCard.left}px`;
        previewDomElm.style.transform = 'translate(0, -50%)';
        
      } else if ( previewOverflowsRight ) {
        previewDomElm.style.left = 'unset';
        previewDomElm.style.right = `${window.innerWidth - hoveredCard.right - 6}px`;
        previewDomElm.style.transform = 'translate(0, -50%)';

      } else {
        previewDomElm.style.left = `${hoveredCard.left + (hoveredCard.width / 2)}px`;
        previewDomElm.style.transform = 'translate(-50%, -50%)';
      }

      // positioning Y
      previewDomElm.style.top = `${hoveredCard.top + window.scrollY + (hoveredCard.height / 2)}px`;

      // set width/height
      previewDomElm.style.width = `${hoveredCard.width}px`;
      previewDomElm.style.height = `${hoveredCard.height}px`;
    }
  },
}
</script>

<style lang="scss" scoped>
#preview {
  position: absolute;
  width: 0;
  left: -100vw;
  z-index: 9;
  transition: width 100ms linear, 
              height 100ms linear, 
              opacity 250ms linear;
  border-radius: .45rem;
  overflow: hidden;
  color: #fff;
  cursor: pointer;
  user-select: none;

  display: flex;
  flex-direction: column;

  .img_wrapper {
    opacity: 0;

    .video_container {
      pointer-events: none;
      position: absolute;
      height: 115%;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      z-index: 1;
      opacity: 0;
    }
  }

  .info_wrapper {
    height: 45%;
    padding: 1.5rem 1.25rem;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    transition: opacity 250ms linear;
    opacity: 0;

    .btn_wrapper {
      display: flex;
    }
    .controls {
      width: 2.5rem;
      height: 2.5rem;
      border-radius: 50%;
      border: transparent;
      margin-right: .5rem;
      margin-bottom: .75rem;
      font-size: 1.05rem;
      cursor: pointer;
      transition: opacity 200ms linear;
      opacity: 0;
    }
    .controls:not(:first-child) {
      background-color: #181818;
      border: rgba(255,255,255,.5) 2px solid;
      color: #fff;
    }
    .controls:last-child {
      margin-left: auto;
    }

    .text_wrapper {
      font-weight: 500;

      > * {
        margin-right: .55rem;
      }
      .compatibility {
        color: #46d369;
        font-weight: 600;
      }
      .age_restriction {
        border: #747474 1px solid;
        padding: .09rem .4rem;
      }
      .video_quality {
        border: #747474 1px solid;
        border-radius: .2rem;
        padding: .09rem .4rem;
        font-size: .7rem;
        color: #e8e8e8;
      }
    }
    .genres_wrapper {
      font-weight: 500;

      .genre {
        padding-right: .75rem;
        margin-right: .75rem;
        display: inline-block;
        position: relative;
      }
      .genre:not(:last-child):after {
        content: '';
        display: inline-block;
        position: absolute;
        top: 50%;
        right: 0%;
        transform: translate(50%, -50%);
        width: 6px;
        height: 6px;
        border-radius: 50%;
        background-color: #646464;
      }
    }
  }


  &.expanded {
    width: 25vw !important;
    height: 25vw !important;
    background-color: #181818;
    box-shadow: rgb(0 0 0 / 75%) 0px 3px 10px;

    .info_wrapper {
      opacity: 1;    
      .controls {
        opacity: 1;

        &:hover {
          border: #fff 2px solid;
        }
      }
    }

    .img_wrapper {
      position: relative;
      overflow: hidden;
      height: 55%;
      opacity: 1;
    }

    img,
    .img_wrapper {
      width: 100%;
    }
    img {
      object-fit: cover;
      height: 100%;
      position: absolute;
      z-index: 2;
    }
  }
}
</style>