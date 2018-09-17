<template>
  <div class="cartcontrol">
    <transition name="move">
      <div class="cart-decrease" v-show="food.count > 0" @click.stop="decreaseCart">
        <i class="inner fa fa-minus-circle"></i>
      </div>
    </transition>
    <div class="cart-count" v-show="food.count > 0">{{food.count}}</div>
    <div class="cart-add fa fa-plus-circle" @click.stop="addCart"></div>
  </div>
</template>

<script>
import Vue from 'vue'
export default {
  props: {
    food: {
      type: Object
    }
  },
  methods: {
    addCart () {
      if (!this.food.count) {
        Vue.set(this.food, 'count', 0)
      }
      this.food.count++
      this.$emit('cart-add', event.target)
    },
    decreaseCart () {
      if (this.food.count > 0) {
        this.food.count--
      }
    }
  }
}
</script>

<style lang="stylus" scoped>
@import '../../../static/css/font-awesome.css'
.cartcontrol
  font-size: 0
  .cart-decrease
    display: inline-block
    padding: 6px
    transform: translate3D(0, 0, 0)
    .inner
      display: inline-block
      line-height: 24px
      font-size: 24px
      color: rgb(0, 160, 220)
      transition: 0.4s all linear
  .cart-count
    display: inline-block
    vertical-align: top
    width: 12px
    padding-top: 6px
    line-height: 24px
    text-align: center
    font-size: 10px
    color: rgb(147, 153, 159)
  .cart-add
    display: inline-block
    padding: 6px
    line-height: 24px
    font-size: 24px
    color: rgb(0, 160, 220)
  .move-enter-active,.move-leave-active
    transition: 0.4 s all linear
  .move-enter-active
    opacity: 1
    transform: translate3D(0, 0, 0)
    .inner
      transform: rotate(0)
  .move-leave-active
    opacity: 0
    transform: translate3D(24px, 0, 0)
    .inner
      transform: rotate(180deg)
  .move-enter,.move-leave
    opacity: 0
    transform: translate3D(24px, 0, 0)
    .inner
      transform: rotate(180deg)
</style>
