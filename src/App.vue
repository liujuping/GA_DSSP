<template>
  <div id="app">

  </div>
</template>

<script>
  export default {
    name: 'app',
    data () {
      return {
        msg: 'Welcome to Your Vue.js App',
        factoryNum: 1,
        machineNum: 6,
        jobNum: 6,
        plan: []
      }
    },
    method: {
      // 初始化染色体
      initChromosome() {

      }
    }
  }
</script>

<style lang="scss">
  img {
    width: 100px;
    float: left;
    margin-right: 10px;
  }

</style>
