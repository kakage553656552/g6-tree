<template>
  <div class="app-container">
    <OrganizationTree />
  </div>
</template>

<script>
import OrganizationTree from './components/OrganizationTree.vue'

export default {
  name: 'App',
  components: {
    OrganizationTree
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html, body {
  width: 100%;
  height: 100%;
  overflow: hidden;
}

.app-container {
  width: 100%;
  height: 100%;
  position: relative;
}
</style> 