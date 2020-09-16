<template lang="pug">

  router-view

</template>
<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'

@Component
export default class App extends Vue {}
</script>
<style lang="scss" src="@/assets/scss/style.scss"></style>
<style lang="scss">
:root {
  height: 100%;
}

body {
  min-height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
