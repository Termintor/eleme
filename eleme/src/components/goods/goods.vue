<template>
  <div class="goods">
    <div class="menu-wrapper" ref="menuWrapper">
      <ul>
      	<li v-for="(item,index) in goods" :class="{'current':currentIndex===index}" @click="selectMenu(index,$event)" class="menu-item">
      	  <span class="text border-1px"><v-icon :typeIndex="item.type" v-if="item.type>0"></v-icon>{{item.name}}</span>
      	</li>
      </ul>
    </div>
    <div class="goods-wrapper" ref="goodsWrapper">
      <ul>
        <li v-for="item in goods" class="good-list good-list-hook">
          <h1 class="title">{{item.name}}</h1>
          <ul>
            <li @click="selectGood(food,$event)" v-for="food in item.foods" class="food-item border-1px">
              <div class="icon">
                <img width="57" height="57" :src="food.icon"></img>
              </div>
              <div class="content">
                <h2 class="name">{{food.name}}</h2>
                <p class="desc">{{food.description}}</p>
                <div class="extra">
                  <span class="count">月售{{food.sellCount}}</span>
                  <span>好评率{{food.rating}}</span>
                </div>
                <div class="price">
                  <span class="now">￥{{food.price}}</span>
                  <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cartcontrol :good="food"></cartcontrol>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <good :good="selectedFood" ref="goodComp"></good>
    <shopcart :selectGoods="selectedFoods" :deliveryPrice="seller.deliveryPrice" :minPrice="seller.minPrice"></shopcart>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll'
  import iconMap from 'components/iconMap/iconMap'
  import shopcart from 'components/shopcart/shopcart'
  import cartcontrol from 'components/cartcontrol/cartcontrol'
  import good from 'components/good/good'

  const ERR_OK = 0
  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data () {
      return {
        goods: [],
        listHeight: [],
        scrollY: 0,
        selectedFood: {}
      }
    },
    computed: {
      currentIndex () {
        for (let i = 0; i < this.listHeight.length; i++) {
          let height1 = this.listHeight[i]
          let height2 = this.listHeight[i + 1]
          if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
            return i
          }
        }
      },
      selectedFoods () {
        let goods = []
        this.goods.forEach((good) => {
          good.foods.forEach((food) => {
            if (food.count) {
              goods.push(food)
            }
          })
        })
        // console.log(goods)
        return goods
      }
    },
    created () {
      this.$http.get('/api/goods').then((response) => {
        response = response.body
        if (response.errno === ERR_OK) {
          this.goods = response.data
          this.$nextTick(() => {
            this._initScroll()
            this._calculateHeight()
          })
          console.log(this.goods)
        }
      })
    },
    methods: {
      selectMenu (index, event) {
        if (!event._constructed) {
          return
        }
        let goodList = this.$refs.goodsWrapper.getElementsByClassName('good-list-hook')
        let el = goodList[index]
        this.goodsScroll.scrollToElement(el, 300)
      },
      selectGood (food, event) {
        if (!event._constructed) {
          return
        }
        this.selectedFood = food
        this.$refs.goodComp.show()
      },
      _initScroll () {
        this.meunScroll = new BScroll(this.$refs.menuWrapper, {
          click: true
        })
        this.goodsScroll = new BScroll(this.$refs.goodsWrapper, {
          probeType: 3,
          click: true
        })

        this.goodsScroll.on('scroll', (pos) => {
          this.scrollY = Math.abs(Math.round(pos.y))
        })
      },
      // 计算右边的每个区间高度
      _calculateHeight () {
        this.goodList = this.$refs.goodsWrapper.getElementsByClassName('good-list-hook')
        let height = 0
        this.listHeight.push(height)
        for (let i = 0; i < this.goodList.length; i++) {
          let item = this.goodList[i]
          height += item.clientHeight
          this.listHeight.push(height)
        }
      }
    },
    components: {
      'v-icon': iconMap,
      shopcart,
      cartcontrol,
      good
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
@import "../../common/stylus/mixin.styl"
  .goods
    display: flex
    position: absolute
    top: 174px
    bottom: 76px
    width: 100%
    overflow: hidden
    .menu-wrapper
      flex: 0 0 80px
      width: 80px
      background: #f3f5f7
      .menu-item
        display: table
        width: 56px
        height: 54px
        padding: 0 12px
        line-height: 14px
        &.current
          position: relative
          z-index: 10
          margin-top: -1px
          background: #fff
          font-weight: 700
          .text
            border: none
        .text
          display: table-cell
          width: 56px
          vertical-align: middle
          border-1px(rgba(7,17,27,0.1))
          font-size: 12px
          font-weight: 200
    .goods-wrapper
      flex: 1
      .good-list
        .title
          padding-left: 14px
          height: 26px
          line-height: 26px
          border-left: 2px solid #d9dde1
          font-size: 12px
          color: rgb(147,153,159)
          background: #f3f5f7
        .food-item
          display: flex
          margin: 18px
          padding-bottom: 18px
          border-1px(rgba(7,17,27,0.1))
          &:last-child
            border-none()
            margin-bottom: 18px
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
              color: rgb(7,17,27)
            .desc, .extra
              line-height: 10px
              font-size: 10px
              color: rgb(147,153,159)
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
                color: rgb(240,20,20)
              .old
                text-decoration: line-through
                font-size: 10px
                color: rgb(147,153,159)
            .cartcontrol-wrapper
              position: absolute
              right: 0
              bottom: 12px
</style>
