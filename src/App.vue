<template>
  <div id="app">
    <m-header></m-header>
    <tab></tab>
<!--    解决切到别的分页，切回来重新加载，发请求的问题-->
    <keep-alive>
      <router-view></router-view>
    </keep-alive>
  </div>
</template>
<script type="text/ecmascript-6">
import MHeader from './components/m-header/m-header.vue'
import Tab from './components/tab/tab.vue'

export default {
  components: {
    MHeader,
    Tab
  }
}
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
</style>
