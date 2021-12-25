<template>
  <header>
    <div class="container">
      <div ref="ciao" class="logo"><img src="../assets/netflix_logo.png" alt=""></div>

      <nav>
        <button id="menu_dropdown_btn" @click="expandMenu">
          menu
          <font-awesome-icon class="icon" :icon="userMenuArrow" />
        </button>
        <ul class="header_nav" ref="header_nav">
          <li><a class="active" href="#">Home</a></li>
          <li><a href="#">TV series</a></li>
          <li><a href="#">Movies</a></li>
          <li><a href="#">Your list</a></li>
          <li><a href="#">Discover</a></li>
          <li><a href="#">Watch again</a></li>
        </ul>
      </nav>      

      <div class="searchBarElm" id="search_wrapper">
        <input :disabled="isSearchDisabled" :class="(isSearchDisabled)? '' : 'active' "
          v-model="searchFilmInput" id="input_search" ref="input_search"
          @keyup.enter="$emit('searchText', searchFilmInput), isSearchDisabled = true" 
          class="searchBarElm" type="text" placeholder="search your show">

        <font-awesome-icon @click="(isSearchDisabled)? isSearchDisabled = false : isSearchDisabled = true" 
         id="search_icon" class="searchBarElm" :icon="searchIcon" />
      </div>

      <div id="notifications">
        <font-awesome-icon id="bell" :icon="notificationsIcon" />
      </div>

      <div id="user_wrapper">
        <img src="../assets/user_n_img.png" alt="">
        <font-awesome-icon class="icon" :icon="userMenuArrow" />
      </div>
    </div>
  </header>
</template>

<script>
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';
import { faSearch, faBell, faCaretDown } from '@fortawesome/free-solid-svg-icons';

export default {
  name: 'Header',
  components: {
    FontAwesomeIcon,
  },

  data() {
    return {
      searchFilmInput: "",
      searchIcon: faSearch,
      notificationsIcon: faBell,
      userMenuArrow: faCaretDown,
      isSearchDisabled: true,
    }
  },

  methods: {
    expandMenu() {
      this.$refs.header_nav.classList.toggle("expanded");
    },
  },

  watch: {
    isSearchDisabled() {
      // when input becomes active focus on it
      this.$nextTick(() => this.$refs.input_search.focus())
      // empty input when becomes inactive
      this.searchFilmInput = "";

      document.querySelector('body').classList.toggle('blur');
    }
  },

  mounted() {
    // on change focus hide search bar
    document.addEventListener('click', (ev) => {
      const clickedOnSearchBar = ev.target.classList.contains('searchBarElm') || ev.target.closest(".searchBarElm");
      
      if ( clickedOnSearchBar ) {
        return null;
      }
      this.isSearchDisabled = true
    });
  }
}
</script>

<style scoped lang="scss">
  header {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    z-index: 99999999;
    padding-bottom: 1rem;
    background: rgb(0,0,0);
    background: linear-gradient(180deg, rgba(0,0,0,1) 3%, rgba(0,150,136,0) 93%); 
  }

  .container {
    display: flex;
    align-items: center;
    @media screen and (max-width: 750px) {
      & {
        padding: 0 5vw;
      }
    }
  }

  .logo {
    img {
      width: 6.25rem;
      margin-right: 1.5rem;
    }

    @media screen and (min-width: 1800px) {
      margin: .75rem 0;
      img {
        width: calc(6.25rem + 1vw);
      }
    }
    @media screen and (min-width: 1800px) {
      margin: .75rem 0;
      img {
        width: calc(6.25rem + 2vw);
      }
    }
  }


  .header_nav {
    display: flex;
    list-style: none;
    margin-left: 1rem;
    a {
      color: rgb(172, 172, 172);
      text-decoration: none;
      margin: 0 .5rem;
      font-weight: 500;
      font-size: .95rem;
    }
    a.active {
      color: white;
    }
    @media screen and (min-width: 1800px) {
      a {
        margin: 0 .5vw;
        font-size: 1.1vw;
      }
    }
    @media screen and (max-width: 1150px) {
      & {
        position: absolute;
        background-color: rgba(0, 0, 0, 0.9);
        left: 0;
        top: 80%;
        width: 30vw;
        flex-direction: column;
        align-items: center;
        height: 0;
        transition: opacity 500ms cubic-bezier(0.075, 0.82, 0.165, 1);
        opacity: .3;
        overflow: hidden;
        li {
          padding: .75rem 0;
          a {
            font-size: .8rem;
          }
        }
      }
      @media screen and (max-width: 800px) {
        & {
          width: 45%;
        }
      }
      @media screen and (max-width: 550px) {
        & {
          width: 60%;
        }
      }

      &.expanded {
        height: unset;
        opacity: 1;
        padding: .5rem 0;
        border: solid 1px rgba(255,255,255,.15);
        border-top: #fff solid 1px;
      }
    }
  }


  #search_wrapper {
    position: relative;
    margin-left: auto;
    @media screen and (min-width: 1800px) {
      margin-right: .3vw;
    }
    @media screen and (min-width: 2600px) {
      margin-right: .55vw;
    }
    input {
      background-color: transparent;
      color: #ffffff;
      width: 0;
      border: none;
      transition: all 300ms linear;
      outline: none;
      padding: .5rem;  
      padding-left: 2.25rem;
      @media screen and (max-width: 600px) {
        & {
          transition: none;
          padding: 0;
        }
        & + #search_icon {
          left: -0.5rem;
        }
      }
      @media screen and (max-width: 900px) {
        padding: .25rem;  
      }
    }
    input.active {
      padding: .5rem;  
      padding-left: 2.25rem;
      border: 1px #ffffff solid;
      border-right: 1px #d1d1d1 solid;
      width: 15rem;
      margin-right: 1rem;
      background-color: rgba(0, 0, 0, 0.7);
      @media screen and (min-width: 1800px) {
        padding: calc(.5rem + .3vw);
        padding-left: calc(2.25rem + .3vw);
        width: calc(15rem + 2vw);
        font-size: 1rem;
      }
      @media screen and (max-width: 600px) {
        & {
          position: fixed;
          left: 50%;
          top: 10%;
          transform: translateX(-50%);
          transition: none;
        }
      }
    }


    #search_icon {  
      position: absolute;
      color: white;
      top: 50%;
      transform: translateY(-50%);
      left: 4%;
      cursor: pointer;
      font-size: 1.2rem;
      @media screen and (min-width: 1800px) {
        font-size: calc(1.15rem + .3vw);
      }
      @media screen and (min-width: 2600px) {
        font-size: calc(1.15rem + .5vw);
      }
    }
  }

  #notifications {
    #bell {
      color: white;
      font-size: 1.2rem;
      @media screen and (min-width: 1800px) {
        font-size: calc(1.2rem + .3vw);
      }
      @media screen and (min-width: 2600px) {
        font-size: calc(1.15rem + .55vw);
      }
    }
    @media screen and (max-width: 900px) {
      & {
        display: none;
      }
    }
  }

  #user_wrapper {
    display: flex;
    align-items: center;
    img {
      width: 1.75rem;
      margin-left: 1.5rem;
      border-radius: .3rem;
    }
    .icon {
      color: #ffffff;
      margin-left: .35rem;
    }
    @media screen and (min-width: 1800px) {
      img {
        width: calc(1.75rem + .3vw);
      }
      .icon {
        font-size: calc(1rem + .3vw);
      }
    }
    @media screen and (min-width: 2600px) {
      img {
        width: calc(1.75rem + .4vw);
        margin-left: 1vw;
      }
      .icon {
        font-size: calc(1rem + .5vw);
      }
    }
  }

  #menu_dropdown_btn {
    color: #fff;
    background-color: transparent;
    border: transparent;
    font-weight: 500;
    font-size: 1.15rem;
    display: none;
    @media screen and (max-width: 1150px) {
      & {
        display: unset;
      }
    }
  }
  
  ::placeholder { /* Chrome, Firefox, Opera, Safari 10.1+ */
    color: #e6e6e6;
    opacity: 1; /* Firefox */
  }
  :-ms-input-placeholder { /* Internet Explorer 10-11 */
    color: #e6e6e6;
  }
  ::-ms-input-placeholder { /* Microsoft Edge */
    color: #e6e6e6;
  }
</style>