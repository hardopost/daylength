<template>
    <div id="main">

        <div id="enter">
            <form id="form">

                <!-- radio buttons for choosing North or South longitude -->
                <label>Longitude </label>
                <input type="radio" class="radio" name="y" v-model="south" v-bind:value="false">
                <label>N</label>
                <input type="radio" class="radio" name="y" v-model="south" v-bind:value="true">
                <label>S</label>
                <div/>
                <!-- input fields for entering longitude's Degrees, Minutes and Seconds-->
                <input class="number" type="number" placeholder="deg" v-model.number="coOrdinates.lonDegrees">
                <input class="number" type="number" placeholder="min" v-model.number="coOrdinates.lonMinutes">
                <input class="number" type="number" placeholder="sec" v-model.number="coOrdinates.lonSeconds">

                <div>

                    <!-- radio buttons for choosing West or East latitude -->
                    <label>Latitude </label>
                    <input type="radio" class="radio" name="x" v-model="west" v-bind:value="true">
                    <label>W</label>
                    <input type="radio" class="radio" name="x" v-model="west" v-bind:value="false">
                    <label>E</label>
                </div>
                <!-- input fields for entering latitude's Degrees, Minutes and Seconds-->
                <input class="number" type="number" placeholder="deg" v-model.number="coOrdinates.latDegrees">
                <input class="number" type="number" placeholder="min" v-model.number="coOrdinates.latMinutes">
                <input class="number" type="number" placeholder="sec" v-model.number="coOrdinates.latSeconds">
                <br>
                <label>Date </label><br><input type="date" v-model="date" placeholder="date">
            </form>
        </div>

        <!-- button when clicked calls getData() method which takes user entered info and makes request from endpoint -->
        <button @click="getData">Get info</button>
        <br>



        <!--to child component Map below, current component passes latitude and longitude from it's data() to child's
         properties lat and lon. Current component also receives data from child component though custom event
          @changedCoordinates, latitude and longitude that originate from @click event on map, so it is like two way data
          binding. In current component the data is used for API get request and in child component data is used for
           moving map and marker around -->
        <Map :lat="latitude" :lon="longitude"
             @changedCoordinates="latitude = $event[0]; longitude = $event[1]; getData2()"/>

        <!-- display of result, should be displayed much better, but it is at least present-->
        <div id="display" v-if="dayInfo != null">
            Sunrise: {{ dayInfo.results.sunrise }} | Sunset {{ dayInfo.results.sunset }} | Day length
            {{ dayInfo.results.day_length }} <br>
            Longitude: {{ longitude }} | Latitude: {{ latitude }} <br>
            Date: {{ date }}
        </div>

    </div>
</template>


<script>
import Map from '../components/Map.vue'
export default {
  name: 'DayLengthEnter',
    components: {
      Map
    },

    props: {
    },

    data: function () {
    return {
        //user entered coordinates are stored here
        coOrdinates: {
            latDegrees: null,
            latMinutes: null,
            latSeconds: null,
            lonDegrees: null,
            lonMinutes: null,
            lonSeconds: null,
        },
        date: new Date().toISOString().slice(0, 10),//date to correct format so it can be used for get request
        dayInfo: null, //API request response info will be stored here
        west: false, //used below in getData method to - sign in format of decimal form if west is true
        south: false, //used below in getData method to - sign in format of decimal form if south is true
        latitude: "-1.219482", //default value at the start of app
        longitude: "47.413220"

    }//return
  }, //data

  methods: {
      /*getData is called with button (Get Info) click above
      * 1. Method uses data() values that are bound to user input of Degrees, Minutes and Second and converts them into
      * decimal form that is needed for endpoint get request.
      * 2. Then makes get request to API
      * 3. Stores API response data into data() variable dayInfo*/
      getData: function () {
          //1. calculating to decimal form
        let latDeg = this.coOrdinates.latDegrees
        let latMin = this.coOrdinates.latMinutes / 60
        let latSec = this.coOrdinates.latSeconds / 3600
        let latDecimal = latDeg + latMin + latSec;
        if(this.west) latDecimal *= (-1); //if user chooses W radio button beside input then latitude coordinate gets negative value
        console.log(this.west)
        let roundedLatDecimal = latDecimal.toFixed(7);
        let lonDeg = this.coOrdinates.lonDegrees
        let lonMin = this.coOrdinates.lonMinutes / 60
        let lonSec = this.coOrdinates.lonSeconds / 3600
        let lonDecimal = lonDeg + lonMin + lonSec
        if(this.south) lonDecimal *= (-1); //if user chooses S radio button beside input then longitude coordinate gets negative value
        console.log(this.south)
        let roundedLonDecimal = lonDecimal.toFixed(7);
        this.latitude = roundedLatDecimal
          this.longitude = roundedLonDecimal

          //2. 3. get request and storing returned data
        let queryString = "https://api.sunrise-sunset.org/json?lat=" + roundedLatDecimal + "&lng=" + roundedLonDecimal + "&date=" + this.date
        fetch(queryString).
        then(response => response.json()).
        then(data => this.dayInfo = data)
        .catch(err => console.log(err.message))
        console.log(queryString)
      },

      /*getData2 is called in <Map> tag above when child component sends data with custom event back to current component,
      * then this method uses current data that is refreshed by data from child component's custom event and uses it to
      * make get request from API and info will be stored in data variable dayInfo*/
      getData2: function () {
          let lat = this.latitude
          let lon = this.longitude
          console.log("get data2", lat, lon)
          let queryString = "https://api.sunrise-sunset.org/json?lat=" + lat + "&lng=" + lon + "&date=" + this.date
          fetch(queryString).
          then(response => response.json()).
          then(data => this.dayInfo = data)
          .catch(err => console.log(err.message))
          console.log(queryString)
      }
    }
  }

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#main {
    width: 90%;
    min-height: 100%;
    padding: 5px;
    background-color: whitesmoke;
    margin: auto auto 10px;
    text-align: center;
    border-radius: 10px;

}
#form {
    background-color: whitesmoke;
    width: 70%;
    margin:10px auto;
}

#display{
    width: 70%;
    margin:0 auto;
    background-color: whitesmoke;
    font-size: 20px;
}

.number {
    border-radius: 5px;
    width: 60px;
    height: 30px;
    font-size: 20px;
    border-color: white;

}

</style>
