<template>
  <div>
    <b-navbar toggleable="lg" type="dark" variant="info">
    <b-navbar-brand href="/">TP Boulangerie</b-navbar-brand>

    <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>

    <b-collapse id="nav-collapse" is-nav>
      <b-navbar-nav class="ml-auto">
        <b-nav-item href="/">Compte-rendu</b-nav-item>
        <b-nav-item href="/stats">Statistiques personnalisables</b-nav-item>
        <b-nav-item href="/dayStats">Statistiques par jour</b-nav-item>
        <b-nav-item href="/yearStats">Statistiques sur l'année</b-nav-item>
        <b-nav-item href="/prediction">Prediction</b-nav-item>
        <b-nav-item href="/tensor">TensorFlow</b-nav-item>
      </b-navbar-nav>
    </b-collapse>
  </b-navbar>
  </div>
</template>

<script>
export default {
  name: 'navbar',
}
</script>
