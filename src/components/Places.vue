<template>
  <input
    id="map-input"
    :class="INPUT_CLASS"
    :placeholder="PLACEHOLDER"
    v-model="address"
  />
  <button :class="BTN_CLASS" @click="getDirectionForMyCurrentLocations">
    <slot name="buttonContent"></slot>
  </button>

  <span :class="ERROR_CLASS">{{ error }}</span>
</template>
<script>
  /* eslint-disable no-undef */
  import { ref } from "vue";
  import { Loader } from "@googlemaps/js-api-loader";

  export default {
    name: "Places",

    props: {
      GOOGLE_MAPS_API_KEY: String,
      INPUT_CLASS: String,
      BTN_CLASS: String,
      ERROR_CLASS: String,
      PLACEHOLDER: String,
    },

    emits: ["getLocation"],

    setup(props, { emit }) {
      const loader = new Loader({
        apiKey: props.GOOGLE_MAPS_API_KEY,
        libraries: ["places"],
      });

      let data = ref({});

      let latitude = ref(null);
      let longitude = ref(null);

      let address = ref(null);
      let interior = ref(null);
      let exteriror = ref(null);
      let street = ref(null);
      let neighborhood = ref(null);
      let municipality = ref(null);
      let postalCode = ref(null);
      let state = ref(null);
      let error = ref(null);
      //let country = ref(null);

      const clear = () => {
        latitude.value = "";
        longitude.value = "";
        address.value = "";
        interior.value = "";
        exteriror.value = "";
        street.value = "";
        neighborhood.value = "";
        municipality.value = "";
        postalCode.value = "";
        state.value = "";
        //country.value = "";
      };

      const getData = (place) => {
        //save details
        var details = place.address_components;
        address.value = place.formatted_address;
        for (let i = 0; i < details.length; i++) {
          var d = details[i];
          switch (d.types[0]) {
            case "street_number":
              exteriror.value = d.long_name;
              break;
            case "route":
              street.value = d.long_name;
              break;
            case "sublocality_level_1":
              neighborhood.value = d.long_name;
              break;
            case "postal_code":
              postalCode.value = d.long_name;
              break;
            case "locality":
              municipality.value = d.long_name;
              break;
            case "administrative_area_level_1":
              state.value = d.long_name;
              break;
            case "country":
              //country.value = d.long_name
              break;
          }
        }

        latitude.value = place.geometry.location.lat();
        longitude.value = place.geometry.location.lng();

        return {
          address: address.value,
          interior: interior.value,
          exteriror: exteriror.value,
          street: street.value,
          neighborhood: neighborhood.value,
          municipality: municipality.value,
          state: state.value,
          postalCode: postalCode.value,
          latitude: latitude.value,
          longitude: longitude.value,
        };
      };

      const getDirectionForMyCurrentLocations = () => {
        if (navigator.geolocation) {
          navigator.geolocation.getCurrentPosition(
            (position) => {
              getAddress(position.coords.latitude, position.coords.longitude);
            },
            (/*error*/) => {
              error.value =
                "Locator is unable to find your address. Please type your address.";
              //error.message;
            }
          );
        } else {
          error.value = "Your browser does not support geolocation API";
        }
      };

      const getAddress = (lat, lng) => {
        let l = new Loader({
          apiKey: props.GOOGLE_MAPS_API_KEY,
          libraries: ["places"],
        });

        l.load().then((google) => {
          let geocoder = new google.maps.Geocoder();
          geocoder.geocode(
            {
              location: { lat: lat, lng: lng },
            },
            (results, status) => {
              if (status === google.maps.GeocoderStatus.OK) {
                //clear all
                clear();
                //set data
                data.value = getData(results[0]);
                //emit event
                emit("getLocation", data.value);
              }
            }
          );
        });
      };

      loader.load().then(() => {
        //autocomplete
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
            //get place
            var place = autocomplete.getPlace();
            //clear all
            clear();
            //set data
            data.value = getData(place);
            //emit event
            emit("getLocation", data.value);
          }
        );
      });

      return { getDirectionForMyCurrentLocations, address, error };
    },
  };
</script>
