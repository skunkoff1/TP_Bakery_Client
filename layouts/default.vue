<template>
  <div>
      <navbar></navbar>
    <Nuxt />
    <!-- <footpage></footpage> -->
  </div>
</template>

<script>
import navbar from '../components/navbar.vue';
// import footpage from '../components/footpage.vue';

export default {
  components: {
    navbar,
    // footpage,
  }
}
</script>

<style>
@import url('../static/variables.css');
</style>
