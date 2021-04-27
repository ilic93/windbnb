<template>
  <div id="app">
    <div class="grey-area"></div>
    <div class="container">
    <nav class="navbar navbar-default">
      <div class="container-fluid">
        <div class="navbar-header">
          <a class="navbar-brand" href="#">
            <img alt="Brand" src="./assets/logo.png">
          </a>
        </div>
        <form class="navbar-right row" @click.prevent="toggleDrawer()">
          <div class="col-xs-5">
            <input type="text" class="form-control" placeholder="City" v-model="searchedCity">
          </div>
          <div class="col-xs-5">
            <input type="text" class="form-control" placeholder="Number of guests" v-model="minGuests">
          </div>
          <div class="col-xs-2">
            <button class="btn btn-default" type="submit">
              <i class="glyphicon glyphicon-search"></i>
            </button>
          </div>
        </form> 
      </div>
    </nav>
    <main id="gallery">
      <div class="top-of-gallery">
        <h2 class="col-xs-6">Stays in Finland</h2>
        <span class="col-xs-6">{{ showStayList.length }} stays</span>
      </div>
      <div class="col-sm-4 col-xs-12" v-for="(stay, index) in showStayList" :key="index">
        <div class="thumbnail">
          <img class="img-rounded" :src="stay.photo">
          <div class="caption">
            <div class="row">
              <p class="col-xs-9 stay-description">
                <span v-if="stay.superHost" class="super-host">Super Host</span>
                {{ stay.type }}{{ stay.beds ? `, ${stay.beds} beds` : ''}}
              </p>
              <p class="col-xs-3">
                <i class="glyphicon glyphicon-star" style="color:red;"></i>
                {{ stay.rating }}
              </p>
            </div>
            <p class="stay-title">{{ stay.title }}</p>
          </div>
        </div>
      </div>
    </main>
    </div>
    <div v-if="showDrawer" class="drawer">
      <div class="drawer-intro">
        Edit your search
        <button type="button" class="btn btn-default" aria-label="Left Align" @click="closeForm()">
          <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
        </button>
      </div>
      <form @submit.prevent="submitForm()" class="container"> 
        <div class="col-sm-5 city-div">
            <label>Location</label>
            <select class="form-control select-city">
              <option v-for="(city, index) in cityList" :key="index" :value="city">
                {{ city }}, Finland
              </option>
            </select>
        </div>
        <div class="col-sm-5 guest-div">
          <label>Guests</label>
          <div type="text" class="form-control select-guest" placeholder="Add guests" @click="toggleGuests = !toggleGuests">
          {{ minGuests || '0' }} guests
          </div>
          <div v-if="toggleGuests">
            <div class="guests-section">
              <h5>Adults</h5>
              <p>Ages 13 or above</p>
              <div class="row">
                <span class="col-xs-3 col-sm-2 left-button">
                  <button type="button" class="btn btn-default btn-number minus-button" @click="decrementAdults()">
                    <span class="glyphicon glyphicon-minus"></span>
                  </button>
                </span>
                <input type="text" class="input-number col-xs-3 col-sm-2" value="0">
                <span class="col-xs-3 col-sm-2">
                  <button type="button" class="btn btn-default btn-number plus-button" @click="incrementAdults()">
                    <span class="glyphicon glyphicon-plus"></span>
                  </button>
                </span>
              </div>
            </div>
            <div class="guests-section">
              <h5>Children</h5>
              <p>Ages 2 - 12</p>
              <div class="row">
                <span class="col-xs-3 col-sm-2 left-button">
                  <button type="button" class="btn btn-default btn-number minus-button" @click="decrementChildren()">
                    <span class="glyphicon glyphicon-minus"></span>
                  </button>
                </span>
                <input type="text" class="input-number-2 col-xs-3 col-sm-2" value="0">
                <span class="col-xs-3 col-sm-2">
                  <button type="button" class="btn btn-default btn-number plus-button" @click="incrementChildren()">
                    <span class="glyphicon glyphicon-plus"></span>
                  </button>
                </span>
              </div>
            </div>
          </div>
        </div>
        <div class="col-sm-2 search-button">
            <button class="btn btn-default" type="submit">
              <i class="glyphicon glyphicon-search"></i>
              Search
            </button>
        </div>
      </form>
    </div>
    <footer>Author: Marko Ilic</footer>
  </div>
</template>

<script>
import stays from './assets/stays.json'

export default {
  name: 'App',
  data() {
    return {
      stayList: stays,
      showDrawer: false,
      searchedCity: '',
      toggleGuests: false,
      minGuests: null
    }
  },
  methods: {
    toggleDrawer() {
      this.showDrawer = true
      document.querySelector('.grey-area').style.display = 'block'
    },
    submitForm() {
      this.closeForm()
      this.searchedCity = document.querySelector('.select-city').value
    },
    closeForm() {
      this.showDrawer = false
      document.querySelector('.grey-area').style.display = 'none'
    },
    incrementAdults() {
      const x = document.querySelector('.input-number').value
      if(x < 10) document.querySelector('.input-number').value++
      this.sumGuests()
    },
    decrementAdults() {
      const x = document.querySelector('.input-number').value
      if(x > 0) document.querySelector('.input-number').value--
      this.sumGuests()
    },
    incrementChildren() {
      const x = document.querySelector('.input-number-2').value
      if(x < 10) document.querySelector('.input-number-2').value++
      this.sumGuests()
    },
    decrementChildren() {
      const x = document.querySelector('.input-number-2').value
      if(x > 0) document.querySelector('.input-number-2').value--
      this.sumGuests()
    },
    sumGuests() {
      this.minGuests = parseFloat(document.querySelector('.input-number-2').value) + parseFloat(document.querySelector('.input-number').value)
    }
  },
  computed: {
    cityList() {
      let cityList = []
      this.stayList.map(x => !cityList.includes(x.city) ? cityList.push(x.city) : '')
      return cityList
    },
    showStayList() {
      let showStayList = [...this.stayList].filter(x => x.city.includes(this.searchedCity) && this.minGuests <= x.maxGuests)
      return showStayList
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  padding-top: 60px;
  min-height: 100vh;
  position: relative;
}

@media only screen and (min-width: 992px) {
  #gallery img {  
  height: 200px;
  }
}

@media only screen and (min-width: 768px) and (max-width: 991px) {
  #gallery img {
  height: 150px;
  }
}

@media only screen and (min-width: 768px) {
  footer {
    position: absolute;
    bottom: 0;
    width: 100%;
  }

  #gallery {
    margin-bottom: 50px;
  }
}

@media only screen and (max-width: 767px) {

  #app .drawer {height: 75vh;}
  #app .drawer form {margin-top: 0;}
  #app .drawer-intro {display: block;}

  .drawer-intro {
    padding: 15px;
    text-align: left;
    font-weight: bold;
    position: relative;
    height: 62px;
  }

  .drawer-intro .btn {
    background-color: rgba(0, 0, 0, 0);
    border: none;
    position: absolute;
    right: 20px;
  }

  .guest-div {
    margin-top: 120px;
  }

  #app .search-button {
    margin-top: 20px;
    border: none;
  }
}

/*------------------------------------------- nav section ---------------------------------------------- */

form > .col-xs-5, form > .col-xs-2,
form > .col-sm-5, form > .col-sm-2 {
  padding-left: 0px;
  padding-right: 0px;
}

.navbar .btn {width: 100%; color: red;}

.navbar-right {padding: 8px;}

/*------------------------------------------- gallery section ------------------------------------------ */

#gallery {
  display: flex;
  flex-wrap: wrap;
}

p.stay-description {text-align: left; color: grey; padding-right: 0;}

.super-host {
  text-transform: uppercase;
  border: 1px solid darkslategrey;
  border-radius: 10px;
  color: darkslategrey;
  padding: 5px;
  font-size: 10px;
  font-weight: bold;
}

.stay-title {
  text-align: left;
  font-weight: bold;
  margin-top: 10px; 
}

.top-of-gallery {width: 100%;}
.top-of-gallery h2 {text-align: left;}
.top-of-gallery span {text-align: right; padding-bottom: 15px; padding-top: 28px;}

/*------------------------------------------------ drawer section --------------------------------------------*/

.drawer {
  position: fixed;
  top: 0;
  height: 50vh;
  width: 100vw;
  z-index: 2;
  background: white;
}

.drawer-intro {display: none;}

.drawer .col-sm-2.search-button {
  border: 1px solid #ccc;
  border-radius: 4px;
}

.drawer .form-control, .drawer .search-button .btn {height: 60px; cursor: pointer;}

.col-sm-2.search-button .btn {
  border-radius: 20px;
  color: white;
  background-color: red;
}

.drawer form {margin-top: 20px;}

.city-div, .guest-div {
  border-radius: 4px;
  padding: 0 5px;
  background-color:#fff;
  position:relative;
}

.city-div {border: 1px solid #ccc;}

.city-div label, .guest-div label {
  font-size: 12px;
  font-weight: bold;
  text-transform: uppercase;
  color: black;
  padding: 0 10px;  
  position: absolute;
  top: 6px;
  left: 6px;
}

.city-div .select-city {
  background-color: #fff;
  border: 0px;
  height: 60px;
}

.guest-div .select-guest.form-control {
  text-align: left;
  padding: 20px 15px;
  font-size: 14px;
  height: 62px;
}

.guests-section .col-sm-2 .minus-button {float: right;}
.guests-section .col-sm-2 .plus-button {float: left;}

.city-div .select-city:focus, 
.city-div .select-city:hover, 
.guest-div .select-guest:hover {
  border: 1px solid black;
  border-radius: 20px;
  box-shadow: 2px 2px 5px black;
}

.guests-section {padding: 15px 10px; text-align: left;}
.guests-section h5 {font-weight: bold;}
.guests-section p {color: grey;}
.guests-section .btn {height: 30px; width:30px; padding: 0; border-radius: 5px !important;}
.guests-section input {border: none; text-align: center; height: 30px;}

/*----------------------------------------------- grey area section------------------------------------ */

.grey-area {
  height: 100vh;
  width: 100vw;
  background: black;
  opacity: 0.4;
  position: fixed;
  top: 0;
  z-index: 1;
  display: none;
}

/*------------------------------------------------- footer section ------------------------------------ */

footer {
  height: 50px;
  padding: 15px;
  background: lightblue;
  color: black;
}

</style>
