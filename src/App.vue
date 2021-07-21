<template>
  <img alt="Vue logo" src="./assets/logo.png" />
  <!--<HelloWorld msg="Welcome to Your Vue.js App" />-->
  <!--<Maps />-->
  <button @getLocation="getLocation">getLocation</button>
  <places-autocomplete STYLECLASS="form-control" GOOGLE_MAPS_API_KEY="AIzaSyAx9RZNXsPuhNuKTR-CwOZvOxlMpgiDBUE"></places-autocomplete>
</template>

<script>
  //import HelloWorld from "./components/HelloWorld.vue";
  //import Maps from "./components/Maps.vue";
  import PlacesAutocomplete from "./components/PlacesAutocomplete.vue";

  export default {
    name: "App",
    components: {
      //HelloWorld,
      //Maps,
      PlacesAutocomplete,
    },

    methods: {
      getLocation(message) {
       console.log(message);
      }
    }
  };
</script>

<style>
  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }
</style>
