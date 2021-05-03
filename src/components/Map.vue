<template>
    <div class="map">
        <!--map itself :center prop gets coordinates from moveMarker method that returns latitude and longitude in correct format,
        coordinates can originate from @click event when user clicks on the map or from user input from parent comp DayLengthEnter-->
        <l-map :zoom="zoom" :center="moveMarker" @click="latLng">
            <!--tile layer, how map looks like, can change that changing some other tile providers url -->
            <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
            <!--marker on the map, marker is moved by moveMarker method that returns coordinates as above, so marker and map center
            position would at the same coordinates-->
            <l-marker :lat-lng="moveMarker"></l-marker>
        </l-map>
    </div>

</template>

<script>

//Vue2Leaflet is a JavaScript library for the Vue framework that wraps Leaflet making it easy to create reactive maps
import L from 'leaflet';
import { LMap, LTileLayer, LMarker } from 'vue2-leaflet';

export default {
    name: "Map",
    components: {
        LMap,
        LTileLayer,
        LMarker,
    },

    props:{
        /*lattude and longitude that are passed from parent component. Even the @click event coordinates at first
        * will be sent to parent component DayLengthEnter then will be passed back here as props*/
        lat: String,
        lon: String
    },

    data: function () {
        return {
            zoom: 5, //zoom level of map
            url: 'http://{s}.tile.osm.org/{z}/{x}/{y}.png', //gets map layer from open street mat
            attribution: '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
        }
    },


    methods: {
        /*method latLng gets called on @click event above inside <l-map> tag when user clicks on the map.
        This method takes event object as argument which contains the info of latitude and longitude of the
        specific location where user clicked on the map.
        It takes latitude and longitude info from the event data stores it into Array variable latlong and sends it
        with custom event called 'changedCoordinates' to the parent component.
        * */
        latLng: function (event) {
            let latitude = event.latlng.lng.toString()
            let longitude = event.latlng.lat.toString()
            let latlong = [latitude, longitude]
            this.$emit('changedCoordinates', latlong)
        }

    },


    computed: {
        /*computed method takes prop values latitude and longitude (in decimal form, but in type String) and uses
        * them as arguments in another function L.latLong(arg1, arg2) which value it returns. L.latLong(arg1, arg2)
        * converts given arguments latitude and longitude into more suitable suitable format for child components LMap
        * and LMarker properties.
        * The method is used in two tags:
        * 1. used inside tag <l-map> above, used with v-bind to pass value to child component prop of
        * "center" that inside it uses the value to display map so that given coordinates are in the middle of map.
        * 2. used inside tag <l-marker> and its value is passed to child's property of "lat-lng" that uses it to place
        * marker on the map according to these specific coordinates.
         */
        moveMarker: function () {
            return L.latLng(this.lon, this.lat)
            }
        }

}//export default



</script>

<style scoped>

.map {
    height: 50vh;
    width: 70vh;
    text-align: center;
    margin: auto;
    border-radius: 5px;
}

</style>