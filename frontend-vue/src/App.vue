<!-- App.vue -->
<template>
  <div id="app">
    <!-- Aquí puedes usar el componente Shark -->
    <router-view/>
  </div>
</template>


<script>
import Shark from './components/Shark.vue'; // Ajusta la ruta según tu estructura de carpetas
import Games from './components/Games.vue';
export default {
  name: 'App',
  components: {
    Shark ,
    Games// Registra el componente Shark en App.vue
  }
}
</script>