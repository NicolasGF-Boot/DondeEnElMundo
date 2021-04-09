<script>
import axios from 'axios';

export default {
  name: 'home',
  props: [ 'isDarkTheme' ],
  data () {
    return {
      pending: false,
      error: null,
      countryInfo: null,
      search: '',
      showFilter: false,
      showAllRegion: false,
      region: '',
      darkMode: false,
    }
  },
  mounted () {

    this.pending = true;
    axios
      .get('https://restcountries.eu/rest/v2/all')
      .then(response => (this.countryInfo = response.data))
      .catch(error => (this.error = error ))
      .finally( () => { this.pending = false });
  },

  filters: {
    formatNumbers (value) {
      return `${value.toLocaleString()}`
    }
  },

  computed: {
    filteredCountries: function () {
      return this.countryInfo.filter((country) => {
        if (this.region === '' || this.region === 'All Regions') {
          return country.name.toLowerCase().match(this.search.toLowerCase());
        } else if (this.search !== '') {
          return country.name.toLowerCase().match(this.search.toLowerCase());
        } else {
          return country.region.match(this.region);
        }
      })
    }
  },

  methods: {
    handleFilterClick () {
      setTimeout(() => {
        this.showFilter = !this.showFilter;
        this.showAllRegion = true;
      })
    }
  }
}
</script>
<template>
  <div class="home" :class="{ darkTheme : isDarkTheme }">
    <div class="container d-flex justify-content-between p-4">
      <div class="bg-white d-flex align-items-center p-3 shadow">
        <i class="fas fa-search"></i>
        <input 
          class="ml-2 border-0" 
          type="text" 
          v-model="search"
          aria-label="Busca un país..."
          placeholder="Busca un país..."
        />
        <ul class="searchResults"></ul>
      </div>
      <div class="dropdownDiv">
        <a 
          class="btn shadow" 
          v-if="!showAllRegion" 
          v-on:click="showFilter = !showFilter">
            Filtrar por Región 
        </a>

        <a 
          class="dropdownBtn" 
          v-else
          v-on:click="showFilter = !showFilter">
            {{ region }} 
        </a>
        <ul v-if="showFilter" class="dropdownUL">
          <li>
            <label for="radioAfrica">Africa
            <input 
              id="radioAfrica" 
              class="dropdownInput" 
              type="radio" 
              name="africa" 
              value="Africa" 
              v-model="region" 
              v-on:click="handleFilterClick"
            />
            </label>
          </li>
          <li>
            <input 
              id="radioAmerica" 
              class="dropdownInput" 
              type="radio" 
              name="america" 
              value="America" 
              v-model="region" 
              v-on:click="handleFilterClick"
            />
            <label for="radioAmerica">America</label>
          </li>
          <li>
            <input 
              id="radioAsia" 
              class="dropdownInput" 
              type="radio" 
              name="asia" 
              value="Asia" 
              v-model="region" 
              v-on:click="handleFilterClick"
            />
            <label for="radioAsia">Asia</label>
          </li>
          <li>
            <input 
              id="radioEurope" 
              class="dropdownInput" 
              type="radio" 
              name="europe" 
              value="Europe" 
              v-model="region" 
              v-on:click="handleFilterClick"
            />
            <label for="radioEurope">Europe</label>
          </li>
          <li>
            <input 
              id="radioOceania" 
              class="dropdownInput" 
              type="radio" 
              name="oceania" 
              value="Oceania" 
              v-model="region" 
              v-on:click="handleFilterClick"
            />
            <label for="radioOceania">Oceania</label>
          </li>

          <li v-if="showAllRegion">
            <input 
              id="radioAll" 
              class="dropdownInput" 
              type="radio" 
              name="all" 
              value="All Regions" 
              v-model="region" 
              v-on:click="handleFilterClick"
            />
            <label for="radioAll">Todas las regiones</label>
          </li>
        </ul>
      </div>
    </div>

    <h1 v-if="error !== null">Sorry, an error has occurred {{error}}</h1>

    <div class="loaderFlex"><div v-if="pending" class="loader"></div></div>

    <div v-if="countryInfo" class="tileGrid">
      <div v-for="country in filteredCountries" class="card shadow border-0 m-3 text-start fs-8" v-bind:key="country.id">
        <router-link 
          :to="{ name: 'country-detail', params: {country: country.name }}" 
          class="linkTile"
        >
          <img v-bind:src="country.flag" alt="Country Flag" class="card-img-top" height="180px">
          <div class="card-body">
            <h1 class="fs-4 fw-bold">{{ country.name }}</h1>
            <p><span><b> Habitantes: </b></span>{{ country.population | formatNumbers }}</p>
            <p><span><b>Region: </b></span> {{ country.region }}</p>
            <p><span><b>Capital:</b> </span> {{ country.capital }}</p>
          </div>
        </router-link>
      </div>
    </div>
  </div>
</template>

<style scoped>

.home {
  background-color: #f9f9f9;
}

svg{
  width: 100%;
}


.searchBar {
  padding: 35px 75px;
  display: flex;
  justify-content: space-between;
  max-width: 1400px;
  margin: 0 auto;
}

.searchContainer {
  border: none;
  border-radius: 5px;
  box-shadow: 1px 1px 7px 0px rgb(0, 0, 0, 0.1);
  width: 500px;
  height: 50px;
  background-color: #fff;
  display: flex;
  align-items: center;
  padding-left: 30px;
}

.searchIcon {
  font-size: 16px;
  color: #848484;
  padding-right: 30px;
}

.searchInput {
  border: none;
  font-size: 14px;
  font-family: 'Nunito Sans', sans-serif;
  width: 420px;
}

.dropdownBtn {
  display: block;
  background: #fff;
  height: 50px;
  width: 160px;
  box-shadow: 1px 1px 7px 0px rgb(0, 0, 0, 0.1);
  padding-right: 30px;
  padding-left: 30px;
  border-radius: 3px;
  cursor: pointer;
  font-weight: 600;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.dropdownBtn::after {
  font-family: "Font Awesome 5 Free";
  font-weight: 900;
  content: "\f107";
}

.dropdownUL {
  padding-left: 0;
  text-align: left;
  background-color: #fff;
  margin-top: 3px;
  padding: 10px 0; 
  box-shadow: 1px 1px 7px 0px rgb(0, 0, 0, 0.1);
  border-radius: 3px;
  position: absolute;
  width: 220px;
  z-index: 1000;
}

.dropdownUL li {
  list-style: none;
  line-height: 2;
  cursor: pointer;
}

.dropdownUL li label {
  cursor: pointer;
  padding: 0 26px;
  display: block;
  width: 148px;
}

.dropdownUL li:hover {
  background-color: #f9f9f9;
}

.dropdownInput {
  height: 1px;
  clip: rect(1px, 1px, 1px, 1px);
  position: absolute;
}

input[type="radio"] {
  -webkit-appearance: radio;
}

/* loading animation */
.loader {
  border: 16px solid #f3f3f3;
  border-top: 16px solid #2b3845; 
  border-radius: 50%;
  width: 120px;
  height: 120px;
  animation: spin 2s linear infinite;
  margin-bottom: 100px;
}
@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}
.loaderFlex {
  display: flex;
  justify-content: center;
}

.tileGrid {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  padding: 25px 50px 0;
  margin: 0 auto;
  max-width: 1450px;
}

.countryTile {
  display: inline-block;
  background-color: #fff;
  width: 300px;
  box-shadow: 1px 1px 7px 0px rgb(0, 0, 0, 0.1);
  border-radius: 3px;
  cursor: pointer;
  text-align: left;
  margin: 0 25px 80px;
  text-decoration: none;
  color: inherit;
  -webkit-animation: fadein 1s; /* Safari, Chrome and Opera > 12.1 */
       -moz-animation: fadein 1s; /* Firefox < 16 */
        -ms-animation: fadein 1s; /* Internet Explorer */
         -o-animation: fadein 1s; /* Opera < 12.1 */
            animation: fadein 1s;
}
@keyframes fadein {
    from { opacity: 0; }
    to   { opacity: 1; }
}

/* Firefox < 16 */
@-moz-keyframes fadein {
    from { opacity: 0; }
    to   { opacity: 1; }
}

/* Safari, Chrome and Opera > 12.1 */
@-webkit-keyframes fadein {
    from { opacity: 0; }
    to   { opacity: 1; }
}

/* Internet Explorer */
@-ms-keyframes fadein {
    from { opacity: 0; }
    to   { opacity: 1; }
}

.linkTile {
  display: inline-block;
  width: 300px;
  height: 365px;
  text-decoration: none;
  color: inherit;
}


.flag {
  height: 180px;
  width: 300px;
  border-radius: 3px 3px 0 0;
}

.text {
  padding-left: 30px;
  padding-right: 20px;
}

.text h1 {
  font-size: 20px;
}

.text p span {
  font-weight: 600;
}

.text p {
  line-height: 1;
}

::placeholder {
  font-weight: 600;
}

/* Dark Theme */
.darkTheme,
.darkTheme .dropdownUL li:hover  {
  background-color: #202c36 ;
}
.darkTheme .card ,
.darkTheme input ,
.darkTheme .bg-white ,
.darkTheme .btn , 
.darkTheme .searchContainer, 
.darkTheme .searchInput,
.darkTheme .dropdownBtn,
.darkTheme .dropdownUL,
.darkTheme .countryTile {
  background-color: #2b3845 !important;
}
.darkTheme svg , 
.darkTheme .btn , 
.darkTheme h1,
.darkTheme p,
.darkTheme .searchIcon, 
.darkTheme .searchInput, 
.darkTheme ::placeholder,
.darkTheme .dropdownBtn,
.darkTheme .dropdownUL {
  color: #fff;
}

.darkTheme .loader {
  border: 16px solid #2b3845;
  border-top: 16px solid #f3f3f3; 
}


@media (max-width: 875px) {
  .tileGrid {
    justify-content: center;
    padding-left: 50px;
  }

  .searchBar {
    flex-direction: column;
    padding: 25px 15px;
  }

  .searchContainer {
    width: inherit;
  }

  .dropdownDiv {
    margin-top: 40px;
  }
}

</style>
