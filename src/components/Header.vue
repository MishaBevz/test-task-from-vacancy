<template>
  <div class="header">
    <div class="header__title">
        <h1>{{ header.title }}</h1>
    </div>
  </div>
</template>

<script>
export default {
  props: ['header'],
  data () {
    return {
      
    }
  }
}
</script>

<style lang="scss" scoped>
  .header {
      .header__title {
          display: flex;
          justify-content: center;
      }
  }

</style>
