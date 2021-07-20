<script>
  /* eslint-disable no-undef */
  import { computed, ref } from "vue";
  import { useGeolocation } from "./../useGeolocation";
  import { Loader } from "@googlemaps/js-api-loader";

  export default {
    name: "Maps",

    setup() {
      const { coords } = useGeolocation();

      const currPos = computed(() => ({
        lat: coords.value.latitude,
        lng: coords.value.longitude,
      }));

      const loader = new Loader({
        apiKey: "AIzaSyAx9RZNXsPuhNuKTR-CwOZvOxlMpgiDBUE",
        libraries: ["places"],
      });

      const mapDiv = ref(null);
      let map = ref(null);
      let { marker } = ref(null);

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

      //init MAP
      loader.load().then(() => {
        map.value = new google.maps.Map(mapDiv.value, {
          //centrado en la posicion actual
          center: currPos.value,
          zoom: 18,
        });

        marker = new google.maps.Marker({
          map: map.value,
          draggable: true,
          animation: google.maps.Animation.DROP,
          position: currPos.value,
        });

        google.maps.event.addListener(marker, "dragend", function() {
          result_lat.value = marker.getPosition().lat();
          result_lng.value = marker.getPosition().lng();
        });

        //autocompletar
        const input = document.getElementById("input-map");
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
            //centrar el mapa en el lugar
            map.value.setCenter(place.geometry.location);
            //poner el marcador en ese lugar
            marker.setPosition(place.geometry.location);
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

            result_lat.value = place.geometry.location.lat();
            result_lng.value = place.geometry.location.lng();
          }
        );
      });

      return {
        mapDiv,
        result_lat,
        result_lng,
        interior,
        exteriror,
        calle,
        colonia,
        municipio,
        estado,
        CP,
      };
    },
  };
</script>

<template>
  <div>
    <input id="input-map" /> <button>h</button>
    <!--<ul>
      <li><input v-model="result_lat" type="text" /></li>
      <li><input v-model="result_lng" type="text" /></li>
      <li><input v-model="interior" type="text" /></li>
      <li><input v-model="exteriror" type="text" /></li>
      <li><input v-model="calle" type="text" /></li>
      <li><input v-model="colonia" type="text" /></li>
      <li><input v-model="municipio" type="text" /></li>
      <li><input v-model="estado" type="text" /></li>
      <li><input v-model="CP" type="text" /></li>
    </ul>-->
    <div ref="mapDiv" style="width: 100%; height: 80vh" />
  </div>
</template>
