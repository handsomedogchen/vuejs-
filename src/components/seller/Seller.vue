<template>
  <div class="seller" ref='seller'>
    <div class="seller-content">
      <div class="overview">
        <h1 class="title">{{Hseller.name}}</h1>
        <div class="desc border-1px">
          <Star :size="36" :score="Hseller.score"></Star>
          <span class="text">({{Hseller.ratingCount}})</span>
          <span class="text">月售{{Hseller.sellCount}}单</span>
        </div>
        <ul class="remark">
          <li class="block">
            <h2>起送价</h2>
            <div class="content">
              <span class="stress">{{Hseller.minPrice}}</span>元
            </div>
          </li>
          <li class="block">
            <h2>商家配送</h2>
            <div class="content">
              <span class="stress">{{Hseller.deliveryPrice}}</span>元
            </div>
          </li>
          <li class="block">
            <h2>平均配送时间</h2>
            <div class="content">
              <span class="stress">{{Hseller.deliveryTime}}</span>元
            </div>
          </li>
        </ul>
        <div class="favorite" @click="toggleFavorite">
          <i class="fa fa-heart" :class="{'ative':favorite}"></i>
          <span class="text">{{favoriteText}}</span>
        </div>
      </div>
      <Split></Split>
      <div class="bulletin">
        <h1 class="title">
          公告与活动
        </h1>
        <div class="content-wrapper borde-1px">
          <p class="content">{{Hseller.bulletin}}</p>
        </div>
        <ul v-if="Hseller.supports" class="supports">
          <li class="supports-item" v-for="(item,key) in Hseller.supports" :key="key">
            <Icon :iconsize="'icon4'" :iconkey="Hseller.supports[key].type"></Icon>
            <span class="text">{{Hseller.supports[key].description}}</span>
          </li>
        </ul>
      </div>
      <Split></Split>
      <div class="pics">
        <h1 class="title">商家实景</h1>
        <div class="pic-wrapper" ref="picWrapper">
          <ul class="pic-list" ref="picList">
            <li class="pic-item" v-for="(pic, key) in Hseller.pics" :key="key">
              <img :src="pic" width="120" height="90"/>
            </li>
          </ul>
        </div>
      </div>
      <Split></Split>
      <div class="info">
        <h1 class="title border-1px">商家信息</h1>
        <ul>
          <li class="info-item border-1px" v-for="(info, key) in Hseller.infos" :key="key">{{info}}</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import {saveToLocal, loadFromLocal} from '../../commom/js/store.js'
import BScroll from 'better-scroll'
import Star from '../star/Star'
import Split from '../split/Split'
import Icon from '../icon/Icon'
export default {
  props: {
    Hseller: {
      type: Object
    }
  },
  data () {
    return {
      favorite: (function () {
        return loadFromLocal(this.Hseller.id, 'favorite', false)
      }.bind(this))()
    }
  },
  watch: {
    Hseller (newHseller, oldHseller) {
      this.$nextTick(function () {
        this._initScroll()
        this._initPics()
      })
    }
  },
  mounted () {
    this.$nextTick(function () {
      this._initScroll()
      this._initPics()
    })
  },
  components: {
    Star,
    Split,
    Icon
  },
  computed: {
    favoriteText () {
      return this.favorite ? '已收藏' : '收藏'
    }
  },
  methods: {
    _initScroll () {
      if (!this.scroll) {
        this.scroll = new BScroll(this.$refs.seller, {
          click: true
        })
      } else {
        this.scroll.refresh()
      }
    },
    _initPics () {
      if (this.Hseller.pics) {
        let picWidth = 120
        let margin = 6
        let width = (picWidth + margin) * this.Hseller.pics.length - margin
        this.$refs.picList.style.width = width + 'px'
        this.$nextTick(function () {
          if (!this.picScroll) {
            this.picScroll = new BScroll(this.$refs.picWrapper, {
              scrollX: true,
              eventPassthrough: 'vertical'
            })
          } else {
            this.picScroll.refresh()
          }
        })
      }
    },
    toggleFavorite () {
      this.favorite = !this.favorite
      saveToLocal(this.Hseller.id, 'favorite', this.favorite)
    }
  }
}
</script>

<style lang="stylus" scoped>
@import '../../../static/css/font-awesome.css'
@import '../../commom/stylus/mixin.styl'
.seller
  position: absolute
  top: 174px
  bottom: 0
  left: 0
  width: 100%
  overflow: hidden
  .overview
    padding: 18px
    .title
      margin-bottom: 8px
      line-height: 14px
      color: rgb(7, 17, 27)
      font-size: 14px
    .desc
      padding-bottom: 18px
      font-size: 0
      border-1px(rgba(7, 17, 27, .1))
      .star
        display: inline-block
        margin-right: 8px
        vertical-align: top
      .text
        display: inline-block
        margin-right: 12px
        line-height: 18px
        vertical-align: top
        font-size: 10px
        color: rgb(77, 85, 93)
    .remark
      display: flex
      padding-top: 18px
      .block
        flex: 1
        text-align: center
        border-right: 1px solid rgba(7, 17, 27, .1)
        &:last-child
          border:none
        h2
          margin-bottom: 4px
          line-height: 10px
          font-size: 10px
          color: rgb(147, 153, 159)
        .content
          line-height: 24px
          font-size: 10px
          color: rgb(7, 17, 27)
          .stress
            font-size: 24px
    .favorite
      position: absolute
      width: 50px
      right: 11px
      top: 18px
      text-align: center
      .fa-heart
        display: block
        margin-bottom: 4px
        font-size: 24px
        line-height: 24px
        color: #d4d6d9
        &.ative
          color: rgb(240, 20, 20)
      .text
        line-height: 10px
        font-size: 10px
        color: rgb(77, 85, 93)
  .bulletin
    padding: 18px 18px 0 18px
    .title
      margin-bottom: 8px
      line-height: 14px
      color: rgb(7, 17, 27)
      font-size: 14px
    .content-wrapper
      padding: 0 12px 16px 12px
      border-1px(rgba(7, 17, 27, .1))
      .content
        line-height: 24px
        font-size: 12px
        color: rgb(240, 20, 20)
    .supports
      .supports-item
        padding: 16px 12px
        border-1px(rgba(7, 17, 27, .1))
        font-size: 0
        &:last-child
          border-none()
        .icon
          width: 16px
          height: 16px
          margin-right: 6px
          /deep/ span
            width: 16px
            height: 16px
            background-size: 16px 16px
        .text
          line-height: 16px
          font-size: 12px
          color: rgb(7, 17, 27)
  .pics
    padding: 18px
    .title
      margin-bottom: 12px
      line-height: 14px
      color: rgb(7, 17, 27)
      font-size: 14px
    .pic-wrapper
      width: 100%
      overflow: hidden
      white-space: nowrap
      .pic-list
        font-size: 0
        .pic-item
          display: inline-block
          margin-right: 6px
          width: 120px
          height: 90px
          &:last-child
            margin: 0
  .info
    padding: 18px 18px 0 18px
    color: rgb(7, 17, 27)
    .title
      padding-bottom: 12px
      line-height: 14px
      border-1px(rgba(7, 17, 27, .1))
      font-size: 14px
    .info-item
      padding: 16px 12px
      line-height: 16px
      border-1px(rgba(7, 17, 27, .1))
      font-size: 12px
      &:last-child
        border-none()
</style>
