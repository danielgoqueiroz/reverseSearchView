<template>
  <b-container class="menu centered">
    <b-button-group>
      <b-button variant="secondary" to="/search">Busca</b-button>
      <b-button variant="secondary" to="/results">Resultados</b-button>
    </b-button-group>
  </b-container>
</template>

<script>
export default {}
</script>

<style>
.menu {
  position: relative;
  overflow: hidden;
}
.centered {
  float: none;
  position: relative;
  text-align: center;
  margin-bottom: 25px;
}
</style>
