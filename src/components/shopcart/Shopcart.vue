<template>
  <div>
    <div class="shopcart">
      <div class="content" @click="toggleList">
          <div class="content-left">
              <div class="logo-wrapper">
                  <div class="logo" :class="{'highlight' : totalCount > 0}">
                      <i class="fa fa-shopping-cart" :class="{'highlight' : totalCount > 0}"></i>
                  </div>
                  <div class="num" v-show="totalPrice > 0">{{totalCount}}</div>
              </div>
              <div class="price"  :class="{'highlight' : totalPrice > 0}">￥{{totalPrice}}元</div>
              <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
          </div>
          <div class="content-right" @click.stop="pay">
            <div class="pay" :class="payClass">
              {{payDesc}}
            </div>
          </div>
      </div>
      <div class="ball-container">
        <transition-group name="drop" @before-enter="beforeEnter" @enter="enter" @after-enter="afterEnter">
          <div v-for="(ball, key) in balls" :key="key" v-show="ball.show" class="ball">
            <div class="inner inner-hook"></div>
          </div>
        </transition-group>
      </div>
      <transition name="fold">
        <div class="shopcart-list" v-show="listShow">
          <div class="list-header">
            <h1 class="title">购物车</h1>
            <span class="empty" @click="empty">清空</span>
          </div>
          <div class="list-content" ref="listContent">
            <ul>
              <li class="food" v-for="(food, key) in selectFoods" :key="key">
                <span class="name">{{food.name}}</span>
                <div class="price">
                  <span>￥{{food.price * food.count}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <Cartcontrol :food='food'></Cartcontrol>
                </div>
              </li>
            </ul>
          </div>
        </div>
      </transition>
    </div>
    <transition name="fade">
      <div class="list-mask" v-show="listShow" @click="hideList">
      </div>
    </transition>
  </div>
</template>

<script>
import Cartcontrol from '../cartcontrol/Cartcontrol'
import BScroll from 'better-scroll'
export default {
  props: {
    selectFoods: {
      type: Array,
      default () {
        return []
      }
    },
    deliveryPrice: {
      type: Number
    },
    minPrice: {
      type: Number
    }
  },
  data () {
    return {
      balls: [
        {
          show: false
        },
        {
          show: false
        },
        {
          show: false
        },
        {
          show: false
        },
        {
          show: false
        }
      ],
      dropBalls: [],
      fold: false
    }
  },
  computed: {
    totalPrice () {
      let total = 0
      this.selectFoods.forEach(function (food) {
        total += food.price * food.count
      })
      return total
    },
    totalCount () {
      let count = 0
      this.selectFoods.forEach(function (food) {
        count += food.count
      })
      return count
    },
    payDesc () {
      if (this.totalPrice === 0) {
        return `￥${this.minPrice}元起送`
      } else if (this.totalPrice < this.minPrice) {
        let diff = this.minPrice - this.totalPrice
        return `还差￥${diff}元起送`
      } else {
        return '去结算'
      }
    },
    payClass () {
      if (this.totalPrice < this.minPrice) {
        return 'not-enough'
      } else {
        return 'enough'
      }
    },
    // listShow: {
    //   get () {
    //     if (this.fold) {
    //       this.$nextTick(function () {
    //         if (!this.listContentScroll) {
    //           this._initScroll()
    //         } else {
    //           this.listContentScroll.refresh()
    //         }
    //       })
    //     }
    //     return this.fold
    //   },
    //   set () {
    //     let show = !this.fold
    //     return show
    //   }
    // }
    listShow () {
      if (this.fold) {
        this.$nextTick(function () {
          if (!this.listContentScroll) {
            this._initScroll()
          } else {
            this.listContentScroll.refresh()
          }
        })
      }
      return this.fold
    }
  },
  watch: {
    totalCount (newtotalCount, oldtotalCount) {
      if (this.totalCount === 0) {
        this.fold = false
      }
    }
  },
  components: {
    Cartcontrol
  },
  methods: {
    _drop (el) {
      for (let i = 0; i < this.balls.length; i++) {
        let ball = this.balls[i]
        if (!ball.show) {
          ball.show = true
          ball.el = el
          this.dropBalls.push(ball)
          return
        }
      }
    },
    beforeEnter (el) {
      let count = this.balls.length
      while (count--) {
        let ball = this.balls[count]
        if (ball.show) {
          let rect = ball.el.getBoundingClientRect()
          let x = rect.left - 32
          let y = -(window.innerHeight - rect.top - 22)
          el.style.display = ''
          el.style.webkitTransform = `translate3d(0, ${y}px, 0)`
          el.style.transform = `translate3d(0, ${y}px, 0)`
          let inner = el.getElementsByClassName('inner-hook')[0]
          inner.style.webkitTransform = `translate3d(${x}px, 0, 0)`
          inner.style.transform = `translate3d(${x}px, 0, 0)`
        }
      }
    },
    enter (el, done) {
      /* eslint-disable no-unused-vars */
      let rf = el.offsetHeight
      this.$nextTick(function () {
        el.style.webkitTransform = 'translate3d(0, 0, 0)'
        el.style.transform = 'translate3d(0, 0, 0)'
        let inner = el.getElementsByClassName('inner-hook')[0]
        inner.style.webkitTransform = 'translate3d(0, 0, 0)'
        inner.style.transform = 'translate3d(0, 0, 0)'
        el.addEventListener('transitionend', done)
      })
    },
    afterEnter (el) {
      let ball = this.dropBalls.shift()
      if (ball) {
        ball.show = false
        el.style.display = 'none'
      }
    },
    _initScroll () {
      this.listContentScroll = new BScroll(this.$refs.listContent, {
        click: true
      })
    },
    toggleList () {
      if (!this.totalCount) {
        return
      }
      this.fold = !this.fold
    },
    hideList () {
      this.fold = false
    },
    empty () {
      this.selectFoods.forEach(function (food) {
        food.count = 0
      })
    },
    pay () {
      if (this.totalPrice < this.minPrice) {
        return
      }
      console.log(`比钱${this.totalPrice}`)
    }
  }
}
</script>

<style lang="stylus" scoped>
@import '../../commom/stylus/mixin.styl'
@import '../../../static/css/font-awesome.css'
.shopcart
  position: fixed
  left: 0
  bottom: 0
  width: 100vw
  height: 48px
  z-index: 50
  background: #000
  .content
    display: flex
    background: #141d27
    font-size: 0
    color: rgba(255,255,255,.4)
    .content-left
      flex: 1
      .logo-wrapper
        display: inline-block
        vertical-align: top
        position: relative
        top: -10px
        margin: 0 12px
        padding: 6px
        width: 56px
        height: 56px
        box-sizing: border-box
        border-radius: 50%
        background: #141d27
        .logo
          width: 100%
          height: 100%
          border-radius: 50%
          text-align: center
          background: #2b343c
          &.highlight
            background: rgb(0, 160, 220)
          .fa-shopping-cart
            line-height: 44px
            font-size: 24px
            color: #80858a
            &.highlight
              color: #fff
        .num
          position: absolute
          top: 0
          right: 0
          height: 16px
          padding: 0 5px
          line-height: 16px
          text-align: center
          border-radius: 16px
          font-size: 9px
          font-weight: 700
          color: #fff
          background: rgb(240, 20, 20)
          box-shadow: 0 4px 8px 0 rgba(0, 0, 0, .4)
      .price
        display: inline-block
        vertical-align: top
        box-sizing: border-box
        margin-top: 12px
        padding-right: 12px
        line-height: 24px
        font-size: 16px
        font-weight: 700
        border-right: 1px solid rgba(255,255,255,.1)
        font-size: 16px
        font-weight: 700
        &.highlight
          color: #fff
      .desc
        display: inline-block
        vertical-align: top
        margin: 12px 0 0 12px
        line-height: 24px
        font-size: 10px
    .content-right
      flex: 0 0 105px
      width: 105px
      .pay
        height: 48px
        line-height: 48px
        text-align: center
        font-size: 12px
        font-weight: 700
        &.not-enough
          background: #2b333b
        &.enough
          background: #00b43c
          color: #fff
  .ball-container
    .ball
      position: fixed
      left: 32px
      bottom: 22px
      z-index: 200
      .inner
        width: 16px
        height: 16px
        border-radius: 50%
        background: rgb(0, 160, 220)
        transition: all .4s linear
  .shopcart-list
    position: absolute
    top: 0
    left: 0
    width: 100%
    z-index: -1
    transform: translate3d(0, -100%, 0)
    .list-header
      height: 40px
      line-height: 40px
      padding: 0 18px
      background: #f3f5f7
      border-bottom: 1px solid rgba(7, 17, 27, .1)
      .title
        float: left
        font-size: 14px
        color: rgb(7, 17, 27)
      .empty
        float: right
        font-size: 12px
        color: rgb(0, 160, 220)
    .list-content
      padding: 0 18px
      max-height: 217px
      overflow: hidden
      background: #fff
      .food
        position: relative
        padding: 12px 0
        box-sizing: border-box
        border-1px(rgba(7, 17, 27, .1))
        .name
          line-height: 24px
          font-size: 14px
          color: rgb(7, 17, 27)
        .price
          position: absolute
          right: 90px
          bottom: 12px
          line-height: 24px
          font-size: 14px
          font-weight: 700
          color: rgb(240, 20, 20)
        .cartcontrol-wrapper
          position: absolute
          right: 0
          bottom: 6px
  .drop-enter-active,.drop-leave-active
    transition: all .4s cubic-bezier(0.49, -0.29, 0.75, 0.41)
  .fold-enter-active,.fold-leave-active
    transition: 0.4s all linear
  .fold-enter-active
    opacity: 1
    transform: translate3d(0, -100%, 0)
  .fold-leave-active
    opacity: 1
    transform: translate3d(0, 0, 0)
  .fold-enter,.fold-leave
    opacity: 1
    transform: translate3d(0, 0, 0)
.list-mask
  position: fixed
  top: 0
  left: 0
  width: 100%
  height: 100%
  background: rgba(7,17,27,.8)
  z-index: 40
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
