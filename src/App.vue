<template>
  <div id="app">
    <main>
      <grade/>
    </main>
  </div>
</template>

<script>
import grade from './components/grade'

export default {
  name: 'app',
  components: {
    grade
  }
}
</script>

<style>
body {
  margin: 0;
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  display: flex;
  justify-content: center;
}

@media only screen and (min-width: 412px)  {
  .app {
    width: 412px !important;
  }
}
</style>
