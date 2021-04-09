<script>
import axios from 'axios';

export default {
  name: 'country-detail',
  props: [ 'isDarkTheme' ],
  data () {
    return {
      pending: false,
      error: null,
      countryInfo: null,
      borderCountries: null,
      alpha3Code: [],
      alpha3CodetoString: [],
    }
  },
  mounted () {
    this.pending = true;
    axios
      .get(`https://restcountries.eu/rest/v2/name/${this.$route.params.country}?fullText=true`)
      .then((response) => {
        (this.countryInfo = response.data)

        this.alpha3Code = this.countryInfo['0'].borders;
        this.alpha3CodetoString = this.alpha3Code.join(';');
        return axios
          .get(`https://restcountries.eu/rest/v2/alpha?codes=${this.alpha3CodetoString}`)
          .then(response => (this.borderCountries = response.data))
          .catch(error => (this.errorBorders = error))
      })
      .catch(error => (this.error = error ))
      .finally( () => { this.pending = false });
  },
  filters: {
    formatNumbers (value) {
      return `${value.toLocaleString()}`
    }
  }
}
</script>

<template>
  <div class="container pt-5" :class="{ darkTheme : isDarkTheme }">
    <div class="container d-flex">
      <a @click="$router.go(-1)" class="btn shadow p5 mb-3"><i class="fas fa-arrow-left" /> Atrás</a>
    </div>
    <div class="container">
    <h1 v-if="error !== null">Oh, ha ocurrido un error {{error}}</h1>

    <div class="loaderFlex"><div v-if="pending" class="loader"></div></div>

    <div v-for="country in countryInfo" class="d-md-flex flex-wrap align-items-sm-start align-items-md-center" v-bind:key="country.id">
      <img v-bind:src="country.flag" alt="Country Flag" class="shadow col-sm-12 col-lg-6">
      <div class="col-sm-12 col-lg-6 p-5">
        <h1 class="text-start mb-4 fw-bold" t-star>{{country.name}}</h1>
        <div class=" d-flex">
          <ul class="list-unstyled text-start col-6">
            <li class="mb-2"><b>Nombre Local:</b> {{country.nativeName}}</li>
            <li class="mb-2"><b>Habitantes:</b> {{country.population | formatNumbers }}</li>
            <li class="mb-2"><b>Region:</b> {{country.region}}</li>
            <li class="mb-2"><b>Continente:</b> {{country.subregion}}</li>
            <li class="mb-2"><b>Capital:</b> {{country.capital}}</li>
          </ul>
          <ul class="list-unstyled text-start col6">
            <li class="mb-2"><b>Dominio Local:</b> {{country.topLevelDomain['0']}}</li>
            <li class="mb-2"><b>Monedas:</b> {{country.currencies['0'].name}}</li>
            <li class="mb-2"><b>Idiomas:</b> 
              <span 
                v-for="(language, index) in country.languages" 
                v-bind:key="index" >
                {{language.name}}<span v-if="index + 1 < country.languages.length">, </span>
              </span>
            </li>
          </ul>
        </div>
        <div class="col-12 text-start">
          <div class="bordersWrapper">
            <!-- Check if borders exist for this country, if no show text-->
            <span class="noBorders" v-if="countryInfo['0'].borders.length === 0">No Border Countries</span>
            <!-- If yes load border countries -->
            <span v-else><b>Paises Fronterizos:</b></span>
            <span v-for="countryBorderDetails in borderCountries" v-bind:key="countryBorderDetails.id" class="borderList">
              <router-link :to="{ name: 'country-detail', params: { country: countryBorderDetails.name }}" class="btn m-1 shadow">
                {{countryBorderDetails.name}} 
              </router-link>
            </span>
          </div>
        </div>
      </div>
    </div>
  </div>
  </div>
</template>

<style scoped>

@import url('https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta3/dist/css/bootstrap.min.css');

img {
  max-width: 100%;
}
/* loading animation */
.loader {
  border: 16px solid #f3f3f3;
  border-top: 16px solid #2b3845; 
  border-radius: 50%;
  width: 120px;
  height: 120px;
  animation: spin 2s linear infinite;
  transition: 1s ease;
}
.borders .loader {
  width: 60px;
  height: 60px;
}
@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}
.loaderFlex {
  display: flex;
  justify-content: center;
}
/* Dark Theme */
.darkTheme  {
  background-color: #202c36;
}

.darkTheme .borderLinks,
.darkTheme .btn {
  background-color: #2b3845;
}

.darkTheme h1,
.darkTheme p,
.darkTheme span,
.darkTheme li,
.darkTheme .borderLinks,
.darkTheme .btn {
  color: #fff;
}

.darkTheme .loader {
  border: 16px solid #2b3845;
  border-top: 16px solid #f3f3f3; 
}


</style>
