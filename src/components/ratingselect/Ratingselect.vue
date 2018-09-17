<template>
  <div class="ratingselect">
    <div class="rating-type border-1px">
      <span @click="select(2, $event)" class="block positive" :class="{'active':typeSelect === 2}">{{desc.all}}<span class="count">{{ratings.length}}</span></span>
      <span @click="select(0, $event)" class="block positive" :class="{'active':typeSelect === 0}">{{desc.positive}}<span class="count">{{positive.length}}</span></span>
      <span @click="select(1, $event)" class="block negative" :class="{'active':typeSelect === 1}">{{desc.negative}}<span class="count">{{negative.length}}</span></span>
    </div>
    <div @click="toggleContent" class="switch" :class="{'on':contentOnly}">
      <i class="fa fa-check-circle"></i><span class="text">只看有内容的评论</span>
    </div>
  </div>
</template>

<script>
const Positive = 0
const Negative = 1
const All = 2
export default {
  data () {
    return {
      typeSelect: this.selectType,
      contentOnly: this.onlyContent
    }
  },
  props: {
    ratings: {
      type: Array,
      default () {
        return []
      }
    },
    selectType: {
      type: Number,
      default: All
    },
    onlyContent: {
      type: Boolean,
      default: false
    },
    desc: {
      type: Object,
      default () {
        return {
          all: '全部',
          positive: '满意',
          negative: '不满意'
        }
      }
    }
  },
  computed: {
    positive () {
      return this.ratings.filter(function (rating) {
        return rating.rateType === Positive
      })
    },
    negative () {
      return this.ratings.filter(function (rating) {
        return rating.rateType === Negative
      })
    }
  },
  methods: {
    select (type, event) {
      this.typeSelect = type
      this.$emit('Ratingtype-select', this.typeSelect)
    },
    toggleContent () {
      this.contentOnly = !this.contentOnly
      this.$emit('content-toggle', this.contentOnly)
    }
  }
}
</script>

<style lang="stylus" scoped>
@import '../../../static/css/font-awesome.css'
@import '../../commom/stylus/mixin.styl'
.ratingselect
  .rating-type
    padding: 18px 0
    margin: 0 18px
    border-1px(rgba(7, 17, 27, .1))
    font-size: 0
    .block
      display: inline-block
      padding: 8px 12px
      margin-right: 8px
      border-radius: 1px
      font-size: 12px
      line-height: 16px
      color: rgb(77, 85, 93)
      &.active
        color: #fff
      .count
        margin-left: 2px
        font-size: 8px
      &.positive
        background: rgba(0, 160, 220, .2)
        &.active
          background: rgba(0, 160, 220, 1)
      &.negative
        background: rgba(77, 85, 93, .2)
        &.active
          background: rgba(77, 85, 93, 1)
  .switch
    padding: 12px 18px
    line-height: 18px
    border-bottom: 1px solid rgba(7, 17, 27, .1)
    color: rgb(147, 153, 159)
    font-size: 0
    &.on
      i
        color: #00c850
    i
      display: inline-block
      vertical-align: top
      margin-right: 4px
      font-size: 18px
    .text
      display: inline-block
      vertical-align: top
      font-size: 12px
</style>
