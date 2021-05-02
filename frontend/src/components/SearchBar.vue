<template>
  <div>
    <div>
      <img alt="Weather icon" src="../assets/weather_icon.png" height="100">
      <header>
      <h1>Welcome to the daylight information page!</h1>
      <p id="title">The day length can be found by inserting coordinates or selecting a place from the map.</p>
  </header>
</div>
    <form>
      <label for="x">Latitude: </label>
      <input id="x" v-model="x_coordinate" placeholder="Example: 36.7201600"/>
      <label for="y">Longitude: </label>
      <input id="y" v-model="y_coordinate" placeholder="Example: -4.4203400"/>
      <label for="date">Date: </label>
      <input id="date" v-model="addedDate" placeholder="Example: 2021-04-30"/>
      <p><em class = "rules">RULES - Latitude range: -90 to 90. Longitude range: -180 to 180. Date cannot be in the future.</em></p>
      
      <label for="date">Date for map: </label>
      <input id="date" v-model="mapDate" placeholder="Example: 2021-04-30"/>
      <p><b class="day_length">{{info}}</b></p>
      <label for="from"> From </label>
      <input type="date" id="from" v-model="from">
      <label for="to"> To </label>
      <input type="date" id="to" v-model="to">
      <button type="button" @click="information">See the results</button> 
    </form>
    <l-map
        :zoom="zoom"
        :center="center"
        v-on:click="addMarker"
        style="height: 250px; width: 250px; left: 0%"
      >
        <l-tile-layer
          :url="url"
          :attribution="attribution"
        />
        <l-marker v-for="(m, index) in markers" :lat-lng="m" v-bind:key="index" @click="removeMarker(index)"/>
      </l-map>
      </div>
</template>

<script>
import axios from 'axios'
import { latLng } from 'leaflet'
import { LMap, LTileLayer, LMarker } from 'vue2-leaflet'

export default {
  name: "SearchBar",
  components: {
    LMap,
    LTileLayer,
    LMarker
  },
  data() {
    return {
      x_coordinate: null,
      y_coordinate: null,
      addedDate: null,
      info: 'Fill the bars first.',
      zoom: 10,
      center: latLng(58.378025, 26.728493),
      url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      attribution:
        '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      markers: [],
      mapDate: null,
      from: null,
      to: null,
      dateArray: [],
    }
  },
  methods: {
    information: function () {
      var todayDate = new Date();
      if (this.from && this.to && this.x_coordinate && this.y_coordinate){
        if (this.x_coordinate < -90 || this.x_coordinate > 90 || this.y_coordinate < -180 || this.y_coordinate > 180){
          this.info = "Check the rules again and modify coordinate values.";
        } else {
        var fromDate = new Date(this.from);
        var toDate = new Date(this.to);
        if (fromDate <= todayDate && toDate <= todayDate) {
          if (toDate < fromDate){
            this.info = 'Please change the order of From and To.';
          } else{
            let x;
            x = latLng(this.x_coordinate, this.y_coordinate);
            this.markers.push(x);
            this.center = x;
            //var dateArray = [];
            //var currentDate = this.fromDate;
            while (fromDate <= toDate) {
              this.dateArray.push(fromDate.toISOString().split('T')[0]);
              fromDate.setDate(fromDate.getDate() + 1);
            }
            var answer = [];
            var i = 0;
            while (i < this.dateArray.length){
              axios
              .get('https://api.sunrise-sunset.org/json?lat=' + this.x_coordinate + '&lng=' + this.y_coordinate + '&date=' + this.dateArray[i])
              .then((response) => {
                      var result = response.data;
                      var finalAnswer = result.results.day_length.substring(0,2) + ' hours ' + result.results.day_length.substring(3,5) + ' minutes and ' + result.results.day_length.substring(6,8) + ' seconds.';
                      answer.push(finalAnswer);
                  })
              .catch(() => {
                  error => console.log(error)
                  })
              i = i + 1;
            }
            this.info = answer;
        }
        } else {
          this.info = 'Cannot get the information from the future.';
          }
        }
      } else{
        todayDate = todayDate.toISOString().split('T')[0].replaceAll('-','');
        
        if (this.x_coordinate && this.y_coordinate && this.addedDate){
          var modifiedThisDate = this.addedDate;
          modifiedThisDate = modifiedThisDate.replaceAll('-','');
          if (this.x_coordinate < -90 || this.x_coordinate > 90 || this.y_coordinate < -180 || this.y_coordinate > 180 || todayDate < modifiedThisDate){
            this.info = 'Check the rules again and modify coordinate and/or date values.'
          } else{
            axios
            .get('https://api.sunrise-sunset.org/json?lat=' + this.x_coordinate + '&lng=' + this.y_coordinate + '&date=' + this.addedDate)
            .then((response) => {
                    var result = response.data;
                    var finalAnswer = 'The sun rised at ' + result.results.sunrise + '. The sunset was at ' + result.results.sunset + '. The day length was ' + result.results.day_length.substring(0,2) + ' hours ' + result.results.day_length.substring(3,5) + ' minutes and ' + result.results.day_length.substring(6,8) + ' seconds.';
                    this.info = finalAnswer;
                    let x;
                    x = latLng(this.x_coordinate, this.y_coordinate);
                    this.markers.push(x);
                    this.center = x;
                })
            .catch(() => {
                error => console.log(error)
                })
        }
        } else if (this.x_coordinate == null || this.y_coordinate == null || this.addedDate == null){
          this.info = 'To get the result, please fill all the bars you want.';
        }
      }
    },
    addMarker (event) {
      if (this.markers.length == 0){
      this.markers.push(event.latlng)
      var latitude = this.markers[0]["lat"];
      var longitude = this.markers[0]['lng'];
      axios
          .get('https://api.sunrise-sunset.org/json?lat=' + latitude + '&lng=' + longitude + '&date=' + this.mapDate)
          .then((response) => {
                  var result = response.data;
                  var finalAnswer = 'The sun rised at ' + result.results.sunrise + '. The sunset was at ' + result.results.sunset + '. The day length was ' + result.results.day_length.substring(0,2) + ' hours ' + result.results.day_length.substring(3,5) + ' minutes and ' + result.results.day_length.substring(6,8) + ' seconds.';
                  this.info = finalAnswer;
              })
          .catch(() => {
              error => console.log(error)
              })
      }

    },
    removeMarker (index) {
      this.markers.splice(index, 1)
    }
  },
}

</script>

<style scoped>

form {
    position: absolute;
    top: 25%;
    left: 20%;
    margin-left: -25px;
    margin: auto;
}


input {
  width: 250px;
  margin: 10px;
}

button {
  width: 150px;
  position: absolute;
  top: 100%;
  left: 30%;
  margin: 10px;
  
  
}

.day_length {
  position: absolute;
  top: 120%;
  left: 20%;
  margin: 30px;

}

p {
  position: static;
  margin: 0px;
}

header {
    position: absolute;
    width: 100%;
    top: 10%;
    left: 30%;
    margin-left: -25px;
    margin: auto;
    z-index: -1;
    
}

.title {
    margin-top:-20px;
}

img {
    position: absolute;
    top: 0;
    left: 45%;
    margin: auto;
}
</style>