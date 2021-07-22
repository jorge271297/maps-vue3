<template>
  <img alt="Vue logo" src="./assets/logo.png" />
  <!--<HelloWorld msg="Welcome to Your Vue.js App" />-->
  <!--<Maps />-->
  <!--<places-autocomplete  v-on:getLocation="getLocation" STYLECLASS="form-control" GOOGLE_MAPS_API_KEY="AIzaSyAx9RZNXsPuhNuKTR-CwOZvOxlMpgiDBUE"></places-autocomplete>-->
  <pre>
    {{ location }}
  </pre>
  <places
    v-on:getLocation="getLocation"
    INPUT_CLASS="form-control"
    BTN_CLASS="btn"
    GOOGLE_MAPS_API_KEY="AIzaSyAx9RZNXsPuhNuKTR-CwOZvOxlMpgiDBUE"
    PLACEHOLDER="ESCRIBE TU DIRECCION"
  >
  <template v-slot:buttonContent>b</template>
  </places>
</template>

<script>
  import Places from "./components/Places.vue";
  //import HelloWorld from "./components/HelloWorld.vue";
  //import Maps from "./components/Maps.vue";
  //import PlacesAutocomplete from "./components/PlacesAutocomplete.vue";

  export default {
    name: "App",
    components: {
      Places,
      //HelloWorld,
      //Maps,
      //PlacesAutocomplete,
    },

    data() {
      return {
        location: {},
      };
    },

    methods: {
      getLocation(message) {
        this.location = message;
      },
    },
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
