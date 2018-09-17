<template>
  <div id="app">
    <Header :Hseller="seller"></Header>
    <div class="tab">
      <div class="tab-item ">
        <router-link to="goods" exact>商品</router-link>
      </div>
      <div class="tab-item">
        <router-link to="ratings">评价</router-link>
      </div>
      <div class="tab-item">
        <router-link to="seller">商家</router-link>
      </div>
    </div>
    <div>
      <keep-alive>
        <router-view :Hseller="seller"></router-view>
      </keep-alive>
    </div>
  </div>
</template>

<script>
import {urlParse} from './commom/js/util.js'
import Header from './components/header/Header.vue'
const errOk = 0
export default {
  data () {
    return {
      seller: {
        id: (function () {
          let queryParam = urlParse()
          return queryParam.id
        })()
      }
    }
  },
  components: {
    Header
  },
  created () {
    this.$http.get('/api/seller?id=' + this.seller.id).then(function (res) {
      if (res.data.errno === errOk) {
        res = res.data
        this.seller = Object.assign({}, this.seller, res.data)
      }
    }.bind(this))
  },
  name: 'App'
}
</script>

<style lang="stylus" scoped>
@import '../src/commom/stylus/mixin.styl'
@import '../src/commom/stylus/base.styl'
@import '../static/css/reset.css'
@import '../static/css/font-awesome.css'
#app
  -webkit-font-smoothing: antialiased
  -moz-osx-font-smoothing: grayscale
  .tab
    display: flex
    width: 100%
    height: 40px
    border-1px(rgba(7,17,27,.1))
    line-height: 40px
    .tab-item
      flex: 1
      text-align: center
      & > a
        display: block
        font-size: 14px
        color: rgb(77,85,93)
        &.active
          color: rgb(240,20,20)
</style>
