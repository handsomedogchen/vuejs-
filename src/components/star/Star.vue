<template>
  <div class="star" :class="starType">
    <span v-for="(itemClass,key) in itemClasses" :class="itemClass" class="star-item" :key='key'></span>
  </div>
</template>

<script>
const Length = 5
const ClsOn = 'on'
const ClsHalf = 'half'
const ClaOff = 'off'
export default {
  props: {
    size: {
      type: Number
    },
    score: {
      type: Number
    }
  },
  computed: {
    starType () {
      return 'star-' + this.size
    },
    itemClasses () {
      let result = []
      let score = Math.floor(this.score * 2) / 2
      let hasDecimal = score % 1 !== 0
      let integer = Math.floor(score)
      for (let i = 0; i < integer; i++) {
        result.push(ClsOn)
      }
      if (hasDecimal) {
        result.push(ClsHalf)
      }
      while (result.length < Length) {
        result.push(ClaOff)
      }
      return result
    }
  }
}
</script>

<style lang="stylus" scoped>
@import "../../commom/stylus/mixin.styl"
.star
  font-size: 0
  .star-item
    display: inline-block
    background-repeat: no-repeat
  &.star-48
    .star-item
      width: 20px
      height: 20px
      margin-right: 22px
      background-size: 20px 20px
      &:last-child
        margin-right: 0
      &.on
        bg-images('star48_on')
      &.half
        bg-images('star48_half')
      &.off
        bg-images('star48_off')
  &.star-36
    .star-item
      width: 15px
      height: 15px
      margin-right: 6px
      background-size: 15px 15px
      &:last-child
        margin-right: 0
      &.on
        bg-images('star36_on')
      &.half
        bg-images('star36_half')
      &.off
        bg-images('star36_off')
  &.star-24
    .star-item
      width: 10px
      height: 10px
      margin-right: 3px
      background-size: 10px 10px
      &:last-child
        margin-right: 0
      &.on
        bg-images('star24_on')
      &.half
        bg-iamges('star24_half')
      &.off
        bg-images('star24_off')
</style>
