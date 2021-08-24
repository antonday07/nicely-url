<template>
  <div id="app">
    <h2 class="heading">Shorten your URL</h2>
    <shorten-url></shorten-url>
  </div>
</template>

<script>
import ShortenUrl from './components/ShortenUrl.vue';

export default {
  name: 'App',
  components: {
    ShortenUrl,
  }
}
</script>

<style>
  @import url('https://fonts.googleapis.com/css2?family=Rubik:wght@300;400;500&display=swap');

  body {
      font-family: 'Rubik', sans-serif;
  }
  #app {
    color: #2c3e50;
  }
</style>
