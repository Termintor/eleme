<template>
  <div class="cartcontrol">
    <transition name="move">
      <div class="cart-decrease icon-remove_circle_outline" v-show="good.count>0" @click.stop.prevent="decreaseCart($event)"></div>
    </transition>
    <div class="cart-count" v-show="good.count>0">{{good.count}}</div>
    <div class="cart-add icon-add_circle" @click.stop.prevent="addCart($event)"></div>
  </div>
</template>

<script type="text/ecmascript-6">
import Vue from 'vue'
export default {
  props: {
    good: {
      type: Object
    }
  },
  created () {
  },
  methods: {
    addCart (event) {
      // console.log('addCart' + event)
      if (!event._constructed) {
        return
      }
      if (!this.good.count) {
        Vue.set(this.good, 'count', 1)
      } else {
        this.good.count++
      }
    },
    decreaseCart (event) {
      // console.log('decreaseCart' + event)
      if (!event._constructed) {
        return
      }
      if (this.good.count) {
        this.good.count--
      }
    }
  }
}
</script>

<style lang="stylus" rel="stylesheet/stylus" scoped>
  .cartcontrol
    font-size: 0
    .cart-decrease
      display: inline-block
      padding: 6px
      line-height: 24px
      font-size: 24px
      color: rgb(0,166,220)
      transform: rotate(180deg)
      &.move-enter-active, &.move-leave-active
        transition: all .5s
      &.move-enter, &.move-leave-active
        opacity: 0
        transform: translate3D(36px,0,0)
    .cart-count
      display: inline-block
      vertical-align: top
      width: 12px
      padding-top: 6px
      line-height: 24px
      text-align: center
      font-size: 10px
      color: rgb(147,153,159)
    .cart-add
      display: inline-block
      padding: 6px
      line-height: 24px
      font-size: 24px
      color: rgb(0,166,220)
</style>
