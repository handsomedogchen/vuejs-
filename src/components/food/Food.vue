<template>
  <transition name="move">
    <div class="food" v-show="showFlag" ref="food">
        <div class="food-content">
          <div class="image-header">
            <img :src="food.image"/>
            <div class="back" @click="hide">
              <i class="fa fa-chevron-left"></i>
            </div>
          </div>
          <div class="content">
            <h1 class="title">{{food.name}}</h1>
            <div class="detail">
              <span class="sell-count">月售{{food.sellCount}}</span>
              <span class="rating">好评率{{food.rating}}</span>
            </div>
            <div class="price">
              <span class="now">￥{{food.price}}</span><span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
            </div>
            <div class="cartcontrol-wrapper">
              <Cartcontrol :food="food" @cart-add="addFoot"></Cartcontrol>
            </div>
            <transition name="fade">
              <div @click="addFirst" class="buy" v-show="!food.count || food.count === 0">加入购物车</div>
            </transition>
          </div>
          <Split v-show="food.info"></Split>
          <div class="info" v-show="food.info">
            <h1 class="title">商品信息</h1>
            <p class="text">{{food.info}}</p>
          </div>
          <Split></Split>
          <div class="rating">
            <h1 class="title">商品评价</h1>
            <Ratingselect @content-toggle="contentToggle" @Ratingtype-select="RatingtypeSelect" :select-type="selectType" :only-content="onlyContent" :desc="desc" :ratings="food.ratings"></Ratingselect>
            <div class="rating-wrapper">
              <ul v-show="food.ratings && food.ratings.length">
                <li v-show="needShow(rating.rateType, rating.text)" v-for="(rating, key) in food.ratings" :key="key" class="rating-item">
                  <div class="user">
                    <span class="name">{{rating.username}}</span>
                    <img class="avatar" width="12" height="12" :src="rating.avatar"/>
                  </div>
                  <div class="time">{{rating.rateTime  | formatDate}}</div>
                  <p class="text">
                    <i class="fa" :class="{'fa-thumbs-up':rating.rateType===0,'fa-thumbs-down':rating.rateType===1}"></i>{{rating.text}}
                  </p>
                </li>
              </ul>
              <div class="no-rating" v-show="!food.ratings || !food.ratings.length">暂无评价</div>
            </div>
          </div>
        </div>
    </div>
  </transition>
</template>

<script>
import BScroll from 'better-scroll'
import Vue from 'vue'
import Cartcontrol from '../cartcontrol/Cartcontrol'
import Split from '../split/Split'
import Ratingselect from '../ratingselect/Ratingselect'
import {formatDate} from '../../commom/js/date.js'
// const Positive = 0
// const Negative = 1
const All = 2
export default {
  props: {
    food: {
      type: Object
    }
  },
  data () {
    return {
      showFlag: false,
      selectType: All,
      onlyContent: false,
      desc: {
        all: '全部',
        positive: '推荐',
        negative: '吐槽'
      }
    }
  },
  components: {
    Cartcontrol,
    Split,
    Ratingselect
  },
  methods: {
    show () {
      this.showFlag = true
      this.selectType = All
      this.onlyContent = false
      this.$nextTick(function () {
        if (!this.scroll) {
          this.scroll = new BScroll(this.$refs.food, {
            click: true
          })
        } else {
          this.scroll.refresh()
        }
      })
    },
    hide () {
      this.showFlag = false
    },
    addFoot (el) {
      this.$emit('cart-add', event.target)
    },
    addFirst (el) {
      if (!this.food.count) {
        Vue.set(this.food, 'count', 0)
      }
      this.food.count++
      this.addFoot(el)
    },
    RatingtypeSelect (type) {
      this.selectType = type
      this.$nextTick(function () {
        this.scroll.refresh()
      })
    },
    contentToggle (data) {
      this.onlyContent = data
      this.$nextTick(function () {
        this.scroll.refresh()
      })
    },
    needShow (type, text) {
      if (this.onlyContent && !text) {
        return false
      }
      if (this.selectType === All) {
        return true
      } else {
        return type === this.selectType
      }
    }
  },
  filters: {
    formatDate (time) {
      let date = new Date(time)
      return formatDate(date, 'yyyy-MM-dd hh:mm')
    }
  }
}
</script>

<style scoped lang="stylus">
@import '../../commom/stylus/mixin.styl'
@import '../../../static/css/font-awesome.css'
.food
  position: fixed
  left: 0
  top: 0
  bottom: 48px
  z-index: 30
  width: 100%
  background: #fff
  transform: translate3d(0, 0, 0)
  .image-header
    position: relative
    width: 100%
    height: 0
    padding-top: 100%
    img
      position: absolute
      top: 0
      left: 0
      width: 100%
      height: 100%
    .back
      position: absolute
      top: 10px
      left: 0
      .fa-chevron-left
        display: block
        padding: 10px
        font-size: 20px
        color: #fff
  .content
    position: relative
    padding: 18px
    .title
      line-height: 14px
      margin-bottom: 8px
      font-size: 14px
      font-weight: 700
      color: rgb(7, 17, 27)
    .detail
      margin-bottom: 18px
      height: 10px
      line-height: 10px
      font-size: 0
      .sell-count,.rating
        font-size: 10px
        color: rgb(147, 153, 159)
      .sell-count
        margin-right: 12px
    .price
      font-weight: 700
      line-height: 24px
      .now
        margin-right: 8px
        font-size: 14px
        color: rgb(240, 20, 20)
      .old
        text-decoration: line-through
        font-size: 10px
        color: rgb(147, 153, 159)
    .cartcontrol-wrapper
      position: absolute
      right: 12px
      bottom: 12px
    .buy
      position: absolute
      right: 18px
      bottom: 18px
      z-index: 10
      height: 24px
      line-height: 24px
      padding: 0 12px
      box-sizing: border-box
      border-radius: 12px
      font-size: 10px
      color: #fff
      background:rgb(0, 160, 220)
  .info
    padding: 18px
    .title
      line-height: 14px
      margin-bottom: 6px
      font-size: 14px
      color: rgb(7, 17, 27)
    .text
      line-height: 24px
      padding: 0 8px
      font-size: 12px
      color: rgb(77, 85, 93)
  .rating
    padding-top: 18px
    .title
      line-height: 14px
      margin-left: 18px
      font-size: 14px
      color: rgb(7, 17, 27)
    .rating-wrapper
      padding: 0 18px
      .rating-item
        position: relative
        padding: 16px 0
        border-1px(rgba(7, 17, 27, .1))
        .user
          position: absolute
          right: 0
          top: 16px
          line-height: 12px
          font-size: 0
          .name
            display: inline-block
            margin-right: 6px
            vertical-align: top
            font-size: 10px
            color: rgb(147, 153, 159)
          .avatar
            border-radius: 50%
        .time
          margin-bottom: 6px
          line-height: 12px
          font-size: 10px
          color: rgb(147, 153, 159)
        .text
          line-height: 16px
          font-size: 12px
          color: rgb(7,17, 27)
          .fa-thumbs-up, .fa-thumbs-down
            margin-right: 4px
            line-height: 16px
            font-size: 12px
          .fa-thumbs-up
            color: rgb(0, 160, 220)
          .fa-thumbs-down
            color: rgb(147, 153, 159)
      .no-rating
        padding: 16px 0
        font-size: 12px
        color: rgb(147, 153, 159)
.move-enter-active,.move-leave-active
  transition: .4s all linear
.move-enter-active
  opacity: 1
  transform: translate3d(0, 0, 0)
.move-leave-active
  opacity: 0
  transform: translate3d(100%, 0, 0)
.move-enter,.move-leave
  opacity: 0
  transform: translate3d(100%, 0, 0)
.fade-enter-active,.fade-leave-active
    transition: .2s all ease
  .fade-enter-active
    opacity: 1
  .fade-leave-active
    opacity: 0
  .fade-enter,.fade-leave
    opacity: 0
</style>
