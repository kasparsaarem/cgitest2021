<template>
<form>
  <label for="from">From:</label>
  <input type="date" id="from" v-model="from">
  <label for="to">To:</label>
  <input type="date" id="to" v-model="to">
  <button type="button" @click="answer">See the results</button> 
  <p><b class="list">{{answersAsList}}</b></p>
</form>
</template>

<script>
import axios from 'axios'

export default {
  name: "Calendar",
  data() {
    return {
      from: null,
      to: null,
      answersAsList: 'Select a date range.'
    }
  },
  answer: function() {
      var todayDate = new Date();
      if (this.from !== null && this.to !== null) {
          var fromDate = new Date(this.from);
          var toDate = new Date(this.to);
          if (fromDate <= todayDate && toDate <= todayDate) {
            var dateArray = new Array();
            var currentDate = this.fromDate;
            while (currentDate <= this.toDate) {
                dateArray.push(new Date (currentDate));
                currentDate = currentDate.addDays(1);
            }
            axios
          .get('https://api.sunrise-sunset.org/json?lat=' + 4.7896 + '&lng=' + 99.9996 + '&date=' + this.addedDate)
          .then((response) => {
                  var result = response.data;
                  var finalAnswer = 'The sun rised at ' + result.results.sunrise + '. The sunset was at ' + result.results.sunset + '. The day legth was ' + result.results.day_length.substring(0,2) + ' hours ' + result.results.day_length.substring(3,5) + ' minutes and ' + result.results.day_length.substring(6,8) + ' seconds.';
                  this.info = finalAnswer;
              })
          .catch(() => {
              error => console.log(error)
              })
          } else {
              this.answersAsList = 'Cannot get the information from the future.';
          }
      } else {
          this.answersAsList = 'Please fill both.';
      }
  }
}
</script>

<style scoped></style>
