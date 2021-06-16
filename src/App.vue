<template>
<div style="height: 100%">
  <div class="container-fluid">
    <div class="container">
      <Pokemons />
    </div>
  </div>
</div>
</template>

<script>
import Pokemons from './components/Pokemons.vue'

export default {
  name: 'App',
  components: {
    Pokemons
  }
}
</script>

<style>

</style>
