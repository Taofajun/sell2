<template>
  <div class="cartcontrol">
    <transition name="move">
      <div class="cart-decrease" @click="decreaseCart($event)" v-show="food.count>0">
        <span class="inner">
           -
        </span>
      </div>
    </transition>
    <div class="cart-count" v-show="food.count>0">
      {{food.count}}
    </div>
    <div class="cart-add" @click.stop.prevent="addCart($event)">
      +
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import Vue from 'vue'
  export default{
    props: {
      food: {
        type: Object
      }
    },
    methods: {
      addCart(event) {
        if (!event._constructed) {
          return
        }
        if (!this.food.count) {
          Vue.set(this.food, 'count', 1)
        } else {
          this.food.count++
        }
        this.$emit('increment', event.target)
      },
      decreaseCart(event) {
        if (!event._constructed) {
          return
        }
        this.food.count--
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .cartcontrol
    font-size 0
    .cart-decrease
      display inline-block
      padding 6px
      transition all .4s linear
      &.move-transition
        opacity 1
        transform: translate3d(0, 0, 0)
      .inner
        font-size 36px
        line-height 24px
        display inline-block
        transition all .4s linear
        transform rotate(0)
      &.move-enter, &.move-leave
        opacity 0
        transform: translate3d(24px, 0, 0)
        .inner
          transform rotate(180deg)
    .cart-count
      font-size 36px
      display inline-block
    .cart-add
      display inline-block
      font-size 36px
      line-height 24px
      padding 6px
</style>

