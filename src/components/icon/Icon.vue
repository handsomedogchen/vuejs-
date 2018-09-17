<template>
    <div class="icon"  :class="iconsize">
        <span :class="classMap[iconkey]"></span>
    </div>
</template>

<script>
export default {
  props: {
    iconkey: {
      type: Number
    },
    iconsize: {
      type: String
    }
  },
  created () {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
  }
}
</script>

<style lang="stylus" scoped>
@import '../../commom/stylus/mixin'
.icon
  display: inline-block
  width: 12px
  height: 12px
  margin-right: 4px
  vertical-align: top
  span
    display: inline-block
    width: 12px
    height: 12px
    background-size: 12px 12px
    background-repeat: no-repeat
.icon1
  span
    bg('1')
.icon2
  span
    bg('2')
.icon3
  span
    bg('3')
.icon4
  span
    bg('4')
</style>
