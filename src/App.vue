<template>
  <content>
    <div id="app" class="container-fluid">
      <Scanner/>
    </div>
  </content>
</template>

<script>
import Scanner from './components/Scanner.vue'

export default {
  name: 'App',
  components: {
    Scanner
  }
}
</script>