<script>
  /* eslint-disable no-undef */
  import { ref } from "vue";
  import { Loader } from "@googlemaps/js-api-loader";

  export default {
    name: "PlacesAutocomplete",

    props: {
      GOOGLE_MAPS_API_KEY: String,
      STYLECLASS: String,
    },

    emits: ["getLocation"],

    setup(props, { emit }) {
      const loader = new Loader({
        apiKey: props.GOOGLE_MAPS_API_KEY,
        libraries: ["places"],
      });

      let data = ref({
        interior: 0,
        exteriror: 0,
        calle: "",
        colonia: "",
        municipio: "",
        estado: "",
        CP: 0,
      });

      let result_lat = ref(null);
      let result_lng = ref(null);

      let interior = ref(null);
      let exteriror = ref(null);
      let calle = ref(null);
      let colonia = ref(null);
      let municipio = ref(null);
      let estado = ref(null);
      let CP = ref(null);

      const clear = () => {
        interior.value = "";
        exteriror.value = "";
        calle.value = "";
        colonia.value = "";
        municipio.value = "";
        estado.value = "";
        CP.value = "";
      };

      loader.load().then(() => {
        //autocompletar
        const input = document.getElementById("map-input");
        const options = {
          componentRestrictions: { country: "mx" },
          types: ["geocode"],
        };

        const autocomplete = new google.maps.places.Autocomplete(
          input,
          options
        );

        google.maps.event.addListener(
          autocomplete,
          "place_changed",
          function() {
            //obtener el lugar
            var place = autocomplete.getPlace();
            //limpiar todo
            clear();
            //guardar detalle
            var detalles = place.address_components;
            for (let i = 0; i < detalles.length; i++) {
              var d = detalles[i];
              switch (d.types[0]) {
                case "street_number":
                  exteriror.value = d.long_name;
                  break;
                case "route":
                  calle.value = d.long_name;
                  break;
                case "sublocality_level_1":
                  colonia.value = d.long_name;
                  break;
                case "postal_code":
                  CP.value = d.long_name;
                  break;
                case "locality":
                  municipio.value = d.long_name;
                  break;
                case "country":
                  //.value = d.long_name
                  break;
              }
            }
            emit("getLocation", "hola");
            result_lat.value = place.geometry.location.lat();
            result_lng.value = place.geometry.location.lng();
          }
        );
      });

      return {
        result_lat,
        result_lng,
        interior,
        exteriror,
        calle,
        colonia,
        municipio,
        estado,
        CP,
        data,
      };
    },
  };
</script>

<template>
  <div>
    <input id="map-input" :class="STYLECLASS" /> <button>h</button>
    <ul>
      <li><input v-model="result_lat" type="text" /></li>
      <li><input v-model="result_lng" type="text" /></li>
      <li><input v-model="interior" type="text" /></li>
      <li><input v-model="exteriror" type="text" /></li>
      <li><input v-model="calle" type="text" /></li>
      <li><input v-model="colonia" type="text" /></li>
      <li><input v-model="municipio" type="text" /></li>
      <li><input v-model="estado" type="text" /></li>
      <li><input v-model="CP" type="text" /></li>
    </ul>
  </div>
</template>
