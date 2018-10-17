<template>
  <div id="app">
    <Header :header="Header"></Header>
    <Content :content="Content"></Content>
  </div>
</template>

<script>

import Header from './components/Header.vue';
import Content from './components/Content.vue';

export default {
  name: 'app',
  data () {
    return {
      Header: {
        title: 'Торговый терминал биржи'
      },
      Content: {
        usdBalance: 100000,
        bbtcBalance: 10,
      }
    }
  },
  components: {
    Header,
    Content
  }
}
</script>

<style lang="scss">
  body {
    font-family: 'Roboto', sans-serif;
  }

</style>
