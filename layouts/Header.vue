<template>
  <div>
    <a-menu v-model="current" mode="horizontal" @click="handleClick">
      <a-menu-item key=""> 首页 </a-menu-item>
      <a-sub-menu key="interface">
        <span slot="title">接口测试</span>
        <a-menu-item key="create"> 创建接口 </a-menu-item>
        <a-menu-item key="list"> 接口列表 </a-menu-item>
      </a-sub-menu>
    </a-menu>
  </div>
</template>

<script>
export default {
  data () {
    return {
      current: ['']
    }
  },
  mounted () {},
  methods: {
    handleClick (e) {
      let path = e.keyPath.reverse()
      path = '/' + path.join('/')
      this.$router.push({
        path
      })
    }
  }
}
</script>
