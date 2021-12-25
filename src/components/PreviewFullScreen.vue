<template>
  <div id="full_screen_preview" @click="closeFullPreview">
    <div id="video_container">
      <youtube :video-id="videoId" ref="yt" id="yt"
        :player-vars="playerVars" 
        style="height: 100%; width: 100%;">
      </youtube>
    </div>    
  </div>
</template>


<script>
import axios from 'axios';

export default {
  name: 'PreviewFullScreen',
  props: ['filmData'],
  data() {
    return {
      // video player
      videoId: null,
      playerVars: {
        autoplay: 1,
        mute: 1,
        controls: 1,
      },
    }
  },
  watch: {
    filmData(val) {
      if (val == undefined || val == null) {
        return;
      }

      const screenWidth = document.querySelector('body').clientWidth;
      if ( screenWidth > 1000 ) {
        return;
      }
      
      this.videoRequest(this.filmData.id).then( (res) => {
        const videoExists = res;
        if ( !videoExists ) {
          return;
        }

        document.querySelector('#full_screen_preview').classList.add('show');
        document.querySelector('body').classList.add('blur');        
      });
    },
  },
  methods: {
    async videoRequest(id) {
      const type = this.filmData.media_type;
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
        return false;
      }


      this.$refs.yt.player.playVideo();
      return true;
    },
    closeFullPreview() {
      document.querySelector('body').classList.remove('blur');
      document.querySelector('#full_screen_preview').classList.remove('show');
      this.$refs.yt.player.pauseVideo();
    },
  },
}
</script>


<style scoped lang="scss">
  #video_container {
    width: 90vw;
    max-width: 1000px;
    height: 80vh;
    @media screen and (max-width: 1000px) {
      & {
        height: 70vh;
      }
    }
    @media screen and (max-width: 800px) {
      & {
        height: 50vh;
      }
    }
    @media screen and (max-width: 600px) {
      & {
        height: 40vh;
      }
    }
    @media screen and (max-width: 450px) {
      & {
        height: 30vh;
      }
    }
  }
  #full_screen_preview {
    position: fixed;
    top: 0;
    left: 0;
    z-index: 99999999;
    height: 100vh;
    width: 100vw;
    background-color: rgba(0, 0, 0, 0.7);
    display: none;
    justify-content: center;
    align-items: center;
    &.show {
      display: flex;
    }
  }
</style>