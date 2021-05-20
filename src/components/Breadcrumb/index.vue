<template>
  <el-breadcrumb class="el-icon-arrow-right" separator-class="el-icon-arrow-right">
    <transition-group name="breadcrumb">
      <el-breadcrumb-item v-for="(item, index) in levelList" :key="item.path">
        {{ item.meta.title }}
      </el-breadcrumb-item>
    </transition-group>
  </el-breadcrumb>
</template>

<script>
export default {
  data() {
    return {
      levelList: null
    }
  },
  watch: {
    $route(route) {
      this.getBreadcrumb()
    }
  },
  created() {
    this.getBreadcrumb()
  },
  methods: {
    getBreadcrumb() {
      // only show routes with meta.title
      let matched = this.$route.matched.filter(item => item.meta && item.meta.title)
      const first = matched[0]
      if (!this.isDashboard(first)) {
        matched = this.getParentLevel(first).concat(matched)
      }
      this.levelList = matched.filter(item => item.meta && item.meta.title !== false)
    },
    isDashboard(route) {
      const name = route && route.name
      if (!name) {
        return false
      }
      return name.trim().toLocaleLowerCase() === 'main'.toLocaleLowerCase() || name.trim().toLocaleLowerCase() === 'home'.toLocaleLowerCase()
    },
    getParentLevel(route) {
      let parentName = route.meta.parentName
      console.log(parentName)
      let parentArr = parentName.split(',')
      let parentRoute = []
      for (let parent of parentArr) {
        if (parent !== null && parent !== 'null' && parent !== '') {
          parentRoute.push({ meta: { title: parent }, path: parent })
        }
      }
      return parentRoute
    }
  }
}
</script>

<style lang="scss" scoped>
.app-breadcrumb.el-breadcrumb {
  display: inline-block;
  font-size: 14px;
  line-height: 50px;
  margin-left: 8px;

  .no-redirect {
    color: #97a8be;
    cursor: text;
  }
}
</style>
