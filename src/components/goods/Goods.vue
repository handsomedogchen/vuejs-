<template>
  <div class="goods" v-if="Hseller">
    <div class="menu-wrapper" ref="menuWrapper">
      <ul>
        <li v-for="(item, key) in goods" class="menu-item" :key="key" :class="{ current: currentIndex === key }" @click="selectMent(key, $event)">
          <span class="text">
          <Icon v-show="item.type > 0" :iconsize="'icon3'" :iconkey="item.type"></Icon>{{item.name}}
          </span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" ref="foodsWrapper">
      <ul>
        <li v-for="(item, key) in goods" class="food-list food-list-hook" :key="key">
          <h1 class="title">{{item.name}}</h1>
          <ul>
            <li @click="selectFood(food, $event)" v-for="(food, key) in item.foods" class="food-item" :key="key">
              <div class="icon" >
                <img width='57' height='57' :src="food.icon"/>
              </div>
              <div class="content">
                <h2 class="name">{{food.name}}</h2>
                <p class="desc">{{food.description}}</p>
                <div class="extra">
                  <span class="count">月售{{food.sellCount}}</span><span>好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span class="now">￥{{food.price}}</span><span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <Cartcontrol @cart-add="cartAdd" :food='food' :count='0'></Cartcontrol>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <Shopcart ref="shopcart" :selectFoods="selectFoods" :deliveryPrice="Hseller.deliveryPrice" :minPrice="Hseller.minPrice"></Shopcart>
    <Food :food="selectedFoot" ref="food" @cart-add="cartAdd"></Food>
  </div>
</template>

<script>
import Icon from '../icon/Icon'
import BScroll from 'better-scroll'
import Shopcart from '../shopcart/Shopcart'
import Cartcontrol from '../cartcontrol/Cartcontrol'
import Food from '../food/Food'
let errOk = 0
export default {
  data () {
    return {
      goods: null,
      listHeight: [],
      scrollY: 1,
      selectedFoot: {}
    }
  },
  props: {
    Hseller: {
      type: Object
    }
  },
  components: {
    Icon,
    Shopcart,
    Cartcontrol,
    Food
  },
  computed: {
    currentIndex () {
      for (let i = 0; i < this.listHeight.length; i++) {
        let heightUplim = this.listHeight[i]
        let heightLowlim = this.listHeight[i + 1]
        if (!heightLowlim || (this.scrollY >= heightUplim && this.scrollY < heightLowlim)) {
          return i
        }
      }
      return 0
    },
    selectFoods () {
      let foods = []
      if (this.goods) {
        this.goods.forEach(function (good) {
          good.foods.forEach(function (food) {
            if (food.count > 0) {
              foods.push(food)
            }
          })
        })
      }
      return foods
    }
  },
  created () {
    this.$http.get('/api/goods').then(function (res) {
      if (res.data.errno === errOk) {
        res = res.data
        this.goods = res.data
        this.$nextTick(function () {
          this._initScroll()
          this._calculateHeight()
        })
      }
    }.bind(this))
  },
  methods: {
    selectFood (food, event) {
      this.selectedFoot = food
      this.$refs.food.show()
    },
    selectMent (key, event) {
      let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook')
      let ele = foodList[key]
      this.foodsScroll.scrollToElement(ele, 300)
    },
    _initScroll () {
      this.meunScroll = new BScroll(this.$refs.menuWrapper, {
        click: true
      })
      this.foodsScroll = new BScroll(this.$refs.foodsWrapper, {
        click: true,
        probeType: 3
      })
      this.foodsScroll.on('scroll', function (pos) {
        this.scrollY = Math.abs(Math.round(pos.y))
      }.bind(this))
    },
    _calculateHeight () {
      let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook')
      let height = 0
      this.listHeight.push(height)
      for (let i = 0; i < foodList.length; i++) {
        let item = foodList[i]
        height += item.clientHeight
        this.listHeight.push(height)
      }
    },
    cartAdd (el) {
      this.drop(el)
    },
    drop (el) {
      this.$nextTick(function () {
        this.$refs.shopcart._drop(el)
      })
    }
  }
}
</script>

<style lang="stylus" scoped>
@import "../../commom/stylus/mixin"
.goods
  display: flex
  position: absolute
  top: 174px
  bottom: 46px
  width: 100vw
  overflow: hidden
  .menu-wrapper
    flex: 0 0 80px
    width: 80px
    background: #f3f5f7
    .menu-item
      display: table
      padding: 0 12px
      height: 54px
      width: 56px
      line-height: 14px
      &.current
        position: relative
        z-index: 10
        margin-top: -1px
        background: #fff
        font-weight: 700
        .text
          border-none()
      .text
        display: table-cell
        width: 56px
        vertical-align: middle
        border-1px(rgba(7,17,27,.1))
        font-size: 12px
  .foods-wrapper
    flex: 1
    .title
      padding-left: 14px
      height: 26px
      line-height: 26px
      border-left: 2px solid #d9dde1
      font-size: 12px
      color: rgb(147, 153, 159)
      background: #f3f5f7
    .food-item
      display: flex
      margin: 18px
      padding-bottom: 18px
      border-1px(rgba(7,17,27,.1))
      &:last-child
        border-none()
        margin-bottom: 0
      .icon
        flex: 0 0 57px
        margin-right: 10px
      .content
        flex: 1
        .name
          margin: 2px 0 8px 0
          height: 14px
          line-height: 14px
          font-size: 14px
          color: rgb(7, 17, 27)
        .desc, .extra
          line-height: 10px
          font-size: 10px
          color: rgb(147, 153, 159)
        .desc
          line-height: 12px
          margin-bottom: 8px
        .extra
          .count
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
          right: 0
          bottom: 12px
</style>
