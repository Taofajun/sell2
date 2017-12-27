<template>
  <div id="app">
    <v-header :seller="seller"></v-header>
    <div class="tab" border-1px>
      <router-link to="/goods" class="tab-item">商品</router-link>
      <router-link to="/ratings" class="tab-item">评论</router-link>
      <router-link to="/seller" class="tab-item">卖家</router-link>
    </div>
    <keep-alive>
      <router-view :seller="seller"></router-view>
    </keep-alive>
  </div>
</template>

<script type="text/ecmascript-6">
  import header from './components/header/header.vue'
  const ERR_OK = 0
  export default{
    data() {
      return {
        seller: {}
      }
    },
    created() {
      this.$http.get('/api/seller').then((response) => {
        response = response.body
        if (response.errno === ERR_OK) {
          this.seller = response.data
        }
      })
    },
    components: {
      'v-header': header
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "./common/stylus/index.styl"
  .tab {
    display: flex
    height: 40px
    line-height: 40px
  }

  .tab-item {
    flex: 1
    text-align: center
    font-size: 14px
    color: rgb(77, 85, 93)
    border-1px(rgba(7, 17, 27, .1))
  }

  .tab .tab-item.active {
    color: rgb(240, 20, 20)
  }

</style>
