<template>
  <header>
    <div class="container">
      <div ref="ciao" class="logo"><img src="../assets/netflix_logo.png" alt=""></div>

      <nav>
        <ul class="header_nav">
          <li><a class="active" href="#">Home</a></li>
          <li><a href="#">Serie TV</a></li>
          <li><a href="#">Film</a></li>
          <li><a href="#">La mia lista</a></li>
          <li><a href="#">Nuovi e popolari</a></li>
          <li><a href="#">Guarda di nuovo</a></li>
        </ul>
      </nav>      

      <div class="searchBarElm" id="search_wrapper">
        <font-awesome-icon @click="(isSearchDisabled)? isSearchDisabled = false : isSearchDisabled = true" 
          id="search_icon" class="searchBarElm" :icon="searchIcon" />

        <input :disabled="isSearchDisabled" :class="(isSearchDisabled)? '' : 'active' "
          v-model="searchFilmInput" id="input_search" ref="input_search" 
          class="searchBarElm" type="text" placeholder="cerca titolo film">
          
        <button :disabled="isSearchDisabled" :class="(isSearchDisabled)? '' : 'active' " 
          @click="$emit('searchText', searchFilmInput), isSearchDisabled = true" 
          class="searchBarElm" id="search_film_btn" ref="search_film_btn">Search</button>
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
  watch: {
    isSearchDisabled() {
      // when input becomes active focus on it
      this.$nextTick(() => this.$refs.input_search.focus())
      // empty input when becomes inactive
      this.searchFilmInput = "";
      this.$refs.search_film_btn.blur();
    }
  },
  mounted() {
    // on change focus hide search bar
    document.addEventListener('click', (ev) => {
      if ( ev.target.classList.contains('searchBarElm') || ev.target.closest(".searchBarElm") ) {
        return null;
      }
      this.isSearchDisabled = true
    });
  }
}
</script>

<style scoped lang="scss">
  .container {
    display: flex;
    align-items: center;
  }

  .logo {
    img {
      width: 100px;
      margin-right: 1.5rem;
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
    }
    a.active {
      color: white;
    }
  }

  #search_wrapper {
    position: relative;
    margin-left: auto;
    input {
      background-color: transparent;
      color: #d1d1d1;
      width: 0;
      border: none;
      padding: .5rem;  
      padding-left: 2.25rem;
      transition: all 300ms linear;
      outline: none;
    }
    button {
      border-radius: 0;
      width: 0;
      color: transparent;
      display: inline-block;
      border: none;
      transition: all 100ms linear, margin-right 0ms linear;
      overflow: hidden;
    }
    input.active {
      border: 1px #ffffff solid;
      border-right: 1px #d1d1d1 solid;
      width: 200px;
    }
    button.active {
      background-color: #d1d1d1;
      border: 1px #d1d1d1 solid;
      width: unset;
      color: black;
      cursor: pointer;
      padding: .5rem;
      margin-right: 1.5rem;
    }

    #search_icon {
      position: absolute;
      color: white;
      top: 50%;
      transform: translateY(-50%);
      left: 4%;
      cursor: pointer;
      font-size: 1.2rem;
    }
  }

  #notifications {
    #bell {
      color: white;
      font-size: 1.2rem;
    }
  }

  #user_wrapper {
    display: flex;
    align-items: center;
    img {
      width: 28px;
      margin-left: 1.5rem;
      border-radius: .3rem;
    }
    .icon {
      color: #ffffff;
      margin-left: .35rem;
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