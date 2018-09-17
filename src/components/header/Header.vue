<template>
  <div class="head"  v-if="Hseller">
    <div class="content-wrapper clearfix">
      <div class="avatar">
        <img width="64" height="64" :src="Hseller.avatar"/>
      </div>
      <div class="content">
        <div class="title">
          <span class="brand"></span>
          <span class="name">{{Hseller.name}}</span>
        </div>
        <div class="description">
          {{Hseller.description}}/{{Hseller.deliveryTime}}分钟送达
        </div>
        <div v-if="Hseller.supports" class="support">
          <Icon :iconsize="'icon1'" :iconkey="Hseller.supports[0].type"></Icon>
          <span class="text">{{Hseller.supports[0].description}}</span>
        </div>
      </div>
      <div v-if="Hseller.supports" class="support-count" @click="showDetail">
        <span class="count">{{Hseller.supports.length}}个</span>
        <i class="fa fa-angle-right"></i>
      </div>
    </div>
    <div class="bulletin-wrapper" @click="showDetail">
      <span class="bulletin-title"></span><span class="bulletin-text">{{Hseller.bulletin}}</span>
      <i class="fa fa-angle-right"></i>
    </div>
    <div class="background">
      <img :src="Hseller.avatar" width="100%" height="100%"/>
    </div>
    <transition name="fade">
      <div class="detail" v-show="detailShow">
        <div class="detail-wrapper clearfix">
          <div class="detail-main">
            <h1 class="name">{{Hseller.name}}</h1>
            <div class="star-wrapper">
              <Star :size='48' :score = 'Hseller.score'></Star>
            </div>
            <Title :wenben = "'商家优惠'"></Title>
            <ul v-if="Hseller.supports" class="supports">
              <li class="supports-item" v-for="(item,key) in Hseller.supports" :key="key">
                <Icon :iconsize="'icon1'" :iconkey="Hseller.supports[key].type"></Icon>
                <span class="text">{{Hseller.supports[key].description}}</span>
              </li>
            </ul>
            <Title :wenben = "'商家公告'"></Title>
            <div class="bulletin">
              <p class="content">{{Hseller.bulletin}}</p>
            </div>
          </div>
        </div>
        <div class="detail-close" @click="hideDetail">
          <i class="fa fa-close"></i>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
import Star from '../star/Star'
import Title from '../title/Title'
import Icon from '../icon/Icon'
export default {
  props: {
    Hseller: {
      type: Object
    }
  },
  data () {
    return {
      detailShow: false
    }
  },
  methods: {
    showDetail () {
      this.detailShow = true
    },
    hideDetail () {
      this.detailShow = false
    }
  },
  components: {
    Star,
    Title,
    Icon
  }
}
</script>

<style lang="stylus" scoped>
@import "../../commom/stylus/mixin"
@import '../../../static/css/font-awesome.css'
.head
  position: relative
  overflow: hidden
  color:  #fff
  background: rgba(7,17,27,.5)
  .content-wrapper
    position: relative
    padding: 24px 12px 18px 24px
    font-size:  0
    .avatar
      display: inline-block
      vertical-align: top
    .content
      display: inline-block
      margin-left: 16px
      .title
        margin: 2px 0 8px 0
        .brand
          display: inline-block
          vertical-align: top
          width: 30px
          height: 18px
          bg-images('brand')
          background-size: 30px 18px
          background-repeat:  no-repeat
        .name
          margin-left: 6px
          font-size: 16px
          line-height: 18px
          font-weight: bold
      .description
        margin-bottom: 10px
        line-height: 12px
        font-size: 12px
      .support
        .text
          line-height: 12px
          font-size: 12px
    .support-count
      position: absolute
      right: 12px
      bottom: 18px
      padding: 0 8px
      height: 24px
      line-height: 24px
      border-radius: 14px
      background: rgba(0,0,0,.2)
      text-align: center
      .count
        vertical-align: top
        font-size: 10px
      .fa
        padding-left: 5px
        font-size: 12px
        line-height: 24px
  .bulletin-wrapper
    position: relative
    height: 28px
    line-height: 28px
    padding: 0 22px 0 12px
    white-space: nowrap
    overflow: hidden
    text-overflow: ellipsis
    background: rgba(7,17,27,.2)
    .bulletin-title
      display: inline-block
      vertical-align: top
      margin-top: 9px
      width: 22px
      height: 12px
      bg-images('bulletin')
      background-size: 22px 12px
      background-repeat: no-repeat
    .bulletin-text
      padding-left: 5px
      vertical-align: top
      font-size: 10px
    .fa
      position: absolute
      font-size: 10px
      right: 12px
      top: 9px
  .background
    position: absolute
    top: 0
    left: 0
    width: 100%
    height: 100%
    z-index: -1
    filter: blur(10px)
  .detail
    position: fixed
    z-index: 100
    top: 0
    left: 0
    width: 100vw
    height: 100%
    overflow: auto
    background: rgba(7,17,27,.8)
    backdrop-filter: blur(10px)
    .detail-wrapper
      min-height: calc(100% - 64px)
      .detail-main
        margin-top: 64px
        padding-bottom: 64px
        .name
          line-height: 16px
          text-align: center
          font-size: 16px
          font-weight: 700
        .star-wrapper
          margin-top: 18px
          padding: 2px 0
          text-align: center
        .supports
          width: 80vw
          margin: 0 auto
          .supports-item
            padding: 0 12px
            margin-bottom: 12px
            font-size: 0
            &:last-child
              margin-bottom: 0
            .icon
              display: inline-block
              width: 16px
              height: 16px
              vertical-align: top
              margin-right: 6px
              background-size: 16px 16px
              background-repeat: no-repeat
              &.decrease
                bg-images('decrease_2')
              &.discount
                bg-images('discount_2')
              &.guarantee
                bg-images('guarantee_2')
              &.invoice
                bg-images('invoice_2')
              &.special
                bg-images('special_2')
            .text
              line-height: 16px
              font-size: 12px
        .bulletin
          width: 80vw
          margin: 0 auto
          .content
            padding: 0 12px
            line-height: 24px
            font-size: 12px
    .detail-close
        position: relative
        width: 24px
        height: 32px
        margin:-64px auto 0
        clear: both
        font-size: 32px
  .fade-enter-active,.fade-leave-active
    transition: .5s all ease
  .fade-enter-active
    background: rgba(7,17,27,.8)
    opacity: 1
  .fade-leave-active
    opacity: 0
  .fade-enter,.fade-leave
    opacity: 0
</style>
