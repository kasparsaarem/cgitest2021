<template>
  <div>
    <Header />
    <SearchBar />
    <AddedMap />
  </div>
</template>

<script>
import Header from './components/Header'
import SearchBar from './components/SearchBar'
import AddedMap from './components/AddedMap'

export default {
  name: 'App',

  components: {
    Header,
    SearchBar,
    AddedMap
  }
}
</script>

<style>

</style>
