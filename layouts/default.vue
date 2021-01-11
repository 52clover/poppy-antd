<template>
  <div id="main-layout">
    <a-layout>
      <a-layout-header  class="fixed"><Header /></a-layout-header>
      <a-layout-content><Nuxt /></a-layout-content>
      <a-layout-footer><Footer /></a-layout-footer>
    </a-layout>
  </div>
</template>

<script>
import Header from './Header.vue'
import Footer from './Footer.vue'
export default {
  components: {
    Header,
    Footer
  }
}
</script>

<style>
html {
  font-family: 'Source Sans Pro', -apple-system, BlinkMacSystemFont, 'Segoe UI',
    Roboto, 'Helvetica Neue', Arial, sans-serif;
  font-size: 16px;
  word-spacing: 1px;
  -ms-text-size-adjust: 100%;
  -webkit-text-size-adjust: 100%;
  -moz-osx-font-smoothing: grayscale;
  -webkit-font-smoothing: antialiased;
  box-sizing: border-box;
}
*,
*:before,
*:after {
  box-sizing: border-box;
  margin: 0;
}
.button--green {
  display: inline-block;
  border-radius: 4px;
  border: 1px solid #3b8070;
  color: #3b8070;
  text-decoration: none;
  padding: 10px 30px;
}
.button--green:hover {
  color: #fff;
  background-color: #3b8070;
}
.button--grey {
  display: inline-block;
  border-radius: 4px;
  border: 1px solid #35495e;
  color: #35495e;
  text-decoration: none;
  padding: 10px 30px;
  margin-left: 15px;
}
.button--grey:hover {
  color: #fff;
  background-color: #35495e;
}

.indent {
  padding-left: 30px;
  padding-right: 30px;
}

.fixed{
  width: 100%;
  position: sticky;
  top: 0px;
  z-index: 999;
  box-shadow: 0px 3px 5px #e0e0e0;
}

#main-layout .ant-layout-header {
  background:#fff;
}
</style>
