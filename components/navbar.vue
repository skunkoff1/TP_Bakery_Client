<template>
  <div>
    <b-navbar toggleable="lg" type="dark" variant="info">
    <b-navbar-brand href="#">TP Boulangerie</b-navbar-brand>

    <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>

    <b-collapse id="nav-collapse" is-nav>
      <b-navbar-nav>
        <b-nav-item href="/">Analyses des ventes</b-nav-item>
        <b-nav-item href="/stats">Statistiques personnalisables</b-nav-item>
        <b-nav-item href="/prediction">Prediction</b-nav-item>
      </b-navbar-nav>

      <!-- Right aligned nav items -->

    </b-collapse>
  </b-navbar>
  </div>
</template>

<script>
export default {
  name: 'navbar',
}
</script>
