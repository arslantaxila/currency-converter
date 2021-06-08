<template>
  <div class="container">
  <Header title="Currency Converter" />
  <Converter/>
  </div>
</template>

<script>
import Header from "./components/Header";
import Converter from "./components/Converter";
export default {
  name: 'App',
  components: {
    Header,
    Converter
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400&display=swap');
</style>
