<template>
  <form>
    <label for="x">Latitude: </label>
    <input id="x" v-model="x_coordinate" placeholder="Example: 36.7201600"/>
    <label for="y">Longitude: </label>
    <input id="y" v-model="y_coordinate" placeholder="Example: -4.4203400"/>
    <label for="date">Date: </label>
    <input id="date" v-model="addedDate" placeholder="Example: 2021-04-30"/>
    <p><em class = "rules">RULES - Latitude range: -90 to 90. Longitude range: -180 to 180. Date cannot be in the future.</em></p>
    <button type="button" @click="information">See the results</button> 
    <p><b class="day_length">Result: {{info}}</b></p>
  </form>
</template>

<script>
import axios from 'axios'

export default {
  name: "SearchBar",
  data() {
    return {
      x_coordinate: null,
      y_coordinate: null,
      addedDate: null,
      info: 'Fill the bars first.'
    }
  },
  methods: {
    information: function () {
      var todayDate = new Date();
      todayDate = todayDate.toISOString().split('T')[0].replaceAll('-','');
      
      if (this.x_coordinate !== null && this.y_coordinate !== null && this.addedDate !== null){
        var modifiedThisDate = this.addedDate;
        modifiedThisDate = modifiedThisDate.replaceAll('-','');
        if (this.x_coordinate < -90 || this.x_coordinate > 90 || this.y_coordinate < -180 || this.y_coordinate > 180 || todayDate < modifiedThisDate){
          this.info = 'Check the rules again and modify the bar values.'
        } else{
        axios
          .get('https://api.sunrise-sunset.org/json?lat=' + this.x_coordinate + '&lng=' + this.y_coordinate + '&date=' + this.addedDate)
          .then((response) => {
                  var result = response.data;
                  var finalAnswer = 'The sun rised at ' + result.results.sunrise + '. The sunset was at ' + result.results.sunset + '. The day legth was ' + result.results.day_length.substring(0,2) + ' hours ' + result.results.day_length.substring(3,5) + ' minutes and ' + result.results.day_length.substring(6,8) + ' seconds.';
                  this.info = finalAnswer;
              })
          .catch(() => {
              error => console.log(error)
              })
      }
      } else if (this.x_coordinate == null || this.y_coordinate == null || this.addedDate == null){
        this.info = 'To get the result, please fill all the bars.';
      }
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

</style>