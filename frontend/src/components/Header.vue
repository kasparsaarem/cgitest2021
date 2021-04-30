<template>
<div>
<img alt="Weather icon" src="../assets/weather_icon.png" height="100">
<header>
    <h1>Welcome to the daylight information page!</h1>
    <p>The day length can be found by inserting coordinates or selecting a place from the map.</p>
  </header>
</div>
</template>

<script>
export default {
  name: "Header",
}
</script>

<style scoped>
header {
    position: absolute;
    width: 100%;
    top: 10%;
    left: 30%;
    margin-left: -25px;
    margin: auto;
    z-index: -1;
    
}

p {
    margin-top:-20px;
}

img {
    position: absolute;
    top: 0;
    left: 45%;
    margin: auto;
}

</style>