<template>
  <div>
    <form @submit.prevent="setMarker">
      <label for="lat">Lat:</label>
      <input type="text" id="lat" name="lat" v-model="lat"><br>
      <label for="long">Long:</label>
      <input type="text" id="long" name="long" v-model="long"><br>
      <input type="submit" value="Submit">
    </form>
    <p>{{markers}}</p>
    <p>{{center}}</p>
    <l-map
      :zoom="zoom"
      :center="center"
      v-on:click="addMarker"
      style="height: 500px; width: 500px"
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
import { latLng } from 'leaflet'
import { LMap, LTileLayer, LMarker } from 'vue2-leaflet'
 
export default {
  name: 'SetBounds',
  components: {
    LMap,
    LTileLayer,
    LMarker
  },
  data () {
    return {
      lat: null,
      long: null,
      zoom: 17,
      center: latLng(58.35335505361013, 25.58759146418459),
      url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      attribution:
        '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      markers: []
    }
  },
  methods: {
    setMarker () {
      let x
      if (this.lat && this.long) {
        x = latLng(this.lat, this.long)
        this.markers.push(x)
        this.center = x
      }
    },
    addMarker (event) {
      this.markers.push(event.latlng)
    },
    removeMarker (index) {
      this.markers.splice(index, 1)
    }
  }
}
</script>