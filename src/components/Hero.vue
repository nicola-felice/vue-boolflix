<template>
  <div id="hero">
    <div ref="imgInfoTrailer" class="imgInfoTrailer show" 
      :style="`background-image: url('https://image.tmdb.org/t/p/original${imgId}');`">
    </div>

    <div class="details_wrapper">
      <h2 id="title">{{targetFilm.name}}</h2>
      <p ref="overview" class="show" id="overview">{{targetFilm.overview}}</p>
      <div>
        <button>
          <font-awesome-icon class="icon" :icon="playIcon" />
          Riproduci
        </button>
        <button class="other_info">
          <font-awesome-icon class="icon info" :icon="infoIcon" />
          Altre info
        </button>
      </div>
    </div>

    <youtube :video-id="videoId" ref="yt" id="yt"
      :player-vars="playerVars"
      tabindex="-1" style='pointer-events: none;position: absolute;z-index: -2;bottom: 0;top: -55%;min-width: 100vw;height: 200%;'
      @error="onVideoError" @ready="onPlayerReady">
    </youtube>

    <button @click="toggleAudio" id="btn_audio_controls">
      <font-awesome-icon v-if="isVideoMuted" class="icon" :icon="volumeOff" />
      <font-awesome-icon v-else class="icon" :icon="volumeOn" />
    </button>
  </div>
</template>


<script>
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';
import { faVolumeUp, faVolumeMute, faPlay, faInfoCircle } from '@fortawesome/free-solid-svg-icons';
import axios from 'axios';

export default {
  name: 'Hero',
  components: {
    FontAwesomeIcon,
  },

  data() {
    return {
      isVideoMuted: true,
      // icons
      volumeOn: faVolumeUp,
      volumeOff: faVolumeMute,
      playIcon: faPlay,
      infoIcon: faInfoCircle,

      trendingTvSeries: [],

      // video player
      videoId: null,
      playerVars: {
        autoplay: 1,
        mute: 1,
        controls: 0,
      },
      targetFilm: [],

      // timeouts
      timeouts: [],
    }
  },

  computed: {
    imgId() {
      return this.targetFilm.backdrop_path;
    },
  },

  methods: {
    onPlayerReady() {
      this.timeouts.push(setTimeout(() => {
        // set video on start
        this.$refs.yt.player.seekTo(0);

        // hide the img to show the video
        this.timeouts.push(setTimeout(() => {
          this.$refs.imgInfoTrailer.classList.remove('show');
          this.$refs.overview.classList.remove('show');
        }, 1500));

        // set a timer to the lenght of the video
        // when video ends show img again
        this.$refs.yt.player.getDuration().then(durationVideo => {
          this.timeouts.push(setTimeout(() => {
            this.$refs.imgInfoTrailer.classList.add('show');
            this.$refs.overview.classList.add('show');
          }, (durationVideo * 1000) - 4500));
        }); 

      }, 5000));
    },
    onVideoError() {
      this.timeouts.forEach( elm => {
        clearTimeout(elm);
      }),
      this.$refs.imgInfoTrailer.classList.add('show');
      this.$refs.overview.classList.add('show');
      console.error('video not found or unable to play');
    },
    toggleAudio() {
      if ( this.isVideoMuted ) {
        this.$refs.yt.player.unMute();
        this.isVideoMuted = false;
      } else {
        this.$refs.yt.player.mute();
        this.isVideoMuted = true;
      }
    },
    async suggestedSeriesRequets() {
      let res = await axios.get('https://api.themoviedb.org/3/trending/tv/week?api_key=ef791ca0153b5b4ddac7daddda0a384a');
      let list = res.data.results;

      // remove those with no image
      list = list.filter( elm => elm.backdrop_path != null );

      this.trendingTvSeries = list;
    },
    async videoRequest() {
      await this.suggestedSeriesRequets();      
      let num =  Math.floor(Math.random() * this.trendingTvSeries.length);
      let targetFilm = this.trendingTvSeries[num];
      let res = await axios.get(`https://api.themoviedb.org/3/tv/${targetFilm.id}/videos?api_key=ef791ca0153b5b4ddac7daddda0a384a`);

      res.data.results.forEach( elm => {
        // find a trailer 
        if ( elm.type.includes("Trailer") && this.videoId == null ) {
          this.videoId = elm.key;
          return;
        }
      });

      // ??
      if ( this.targetFilm == null ) {
        this.videoRequest();
      } else {
        this.targetFilm = targetFilm;
      }
      // ??
    },
  },

  created() {
    this.videoRequest();
  }
}
</script>


<style lang="scss" scoped>

  #hero {
    position: relative;
    padding-bottom: 56.25%;
    margin-bottom: 2rem;
    overflow: hidden;
    margin-bottom: -20%;
  }
  .details_wrapper {
    position: absolute;
    z-index: 2;
    bottom: 36%;
    margin-left: 3vw;
    width: 35%;
    color: #fff;
    h2 {
      font-size: 3.5vw;
      line-height: 3.4rem;
      margin-bottom: .75rem;
      font-weight: 800;
      letter-spacing: .25rem;
      text-shadow: 3px 3px 11px rgba(0, 0, 0, .6);
      position: relative;
      &::after {
        content: 'Series';
        font-size: .9rem;
        color: rgba(255, 255, 255, 0.7);
        font-weight: 800;
        padding-left: 35px;
        display: inline-block;
        line-height: 30px;

        position: absolute;
        top: -2rem;
        left: -1rem;

        background-position: left;
        background-repeat: no-repeat;
        background-size: contain;
        background-image: url('../assets/netflix.png');
      }
    }
    #overview {
      font-size: 1.35vw;
      font-weight: 500;
      line-height: 1.75rem;
      text-shadow: 3px 3px 11px rgba(0, 0, 0, .6);
      overflow: hidden;
      max-height: 0;
      opacity: 0;
      transition: max-height 600ms cubic-bezier(0.075, 0.82, 0.165, 1), opacity 300ms linear;
      &.show {
        max-height: 100vh;
        opacity: 1;
        display: -webkit-box;
        margin-bottom: .75rem;
        -webkit-line-clamp: 4;
        -webkit-box-orient: vertical;  
      }
    }
    button {
      margin-top: 1rem;
      padding: .85rem 2.4rem;
      font-size: 1.1rem;
      font-weight: 700;
      margin-right: .75rem;
      cursor: pointer;

      border-radius: .25rem;
      border: transparent;
      display: inline-flex;
      align-items: center;
      &:hover {
        background-color: rgba(255, 255, 255, 0.76);
      }
      &.other_info {
        background-color: rgba(109,109,110,0.7);
        color: white;
        &:hover {
          background-color: rgba(109,109,110,0.4);
        }
      }
      .icon {
        margin-right: .75rem;
        font-size: 1.25rem;
        &.info {
          transform: scale(1.2);
        }
      }
    }
  }
  .imgInfoTrailer {
    opacity: 0;
    width: 100%;
    height: 100%;
    position: absolute;
    z-index: -1;
    background-position: center;
    background-size: cover;

    transition: opacity 500ms linear;
    &.show {
      opacity: 1;
    }
  }
  #btn_audio_controls {
    position: absolute;
    bottom: 36%;
    right: 2.5%;
    z-index: 2;
    cursor: pointer;

    height: 40px;
    width: 40px;
    border-radius: 50%;
    color: #fff;
    font-size: 1rem;

    background-color: transparent;
    border: 1px solid #fff;
    transition: background-color 50ms ease-in-out;
    &:hover {
      background-color: rgba(255, 255, 255, 0.315);
    }
  }

</style>