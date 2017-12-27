<template>
  <div class="foodN" v-show="showFlag" ref="foodN">
    <transition name="move">
      <div class="food">
        <div class="food-content">
          <div class="image-header">
            <img :src="food.image" alt="">
            <span class="back" @click="hide">返回</span>
          </div>
          <div class="content">
            <h1 class="title">
              {{food.name}}
            </h1>
            <div class="detail1">
            <span class="sell-count">
              月售{{food.sellCount}}份
            </span>
            <span class="rating">
              好评率{{food.rating}}%
            </span>
            </div>
            <div class="price">
            <span class="now">
              ￥{{food.price}}
            </span>
            <span class="old" v-show="food.oldPrice">
              ￥{{food.oldPrice}}
            </span>
            </div>
            <div class="cartcontrol-wrapper">
              <cartcontrol :food="food"></cartcontrol>
            </div>
            <transition name="fade">
              <div class="buy" @click.stop.prevent="addFirst" v-show="!food.count||food.count===0">
                加入购物车
              </div>
            </transition>
          </div>
        </div>
        <split v-show="food.info"></split>
        <div class="info">
          <h1 class="title">
            商品信息
          </h1>
          <p class="text" v-show="food.info">
            {{food.info}}
          </p>
        </div>
        <split></split>
        <div class="rating">
          <h1 class="title">商品评价</h1>
          <ratingSelect @toggle="ratingToggle" @select="ratingSelect" :select-type="selectType"
                        :only-content="onlyContent" :desc="desc"
                        :ratings="food.ratings"></ratingSelect>
          <div class="rating-wrapper">
            <ul v-show="food.ratings && food.ratings.length">
              <li v-show="needShow(rating.rateType,rating.text)" class="rating-item border-1px"
                  v-for="rating in food.ratings">
                <div class="user">
                  <span class="name">{{rating.username}}</span>
                  <img class="avatar" width="12" height="12" :src="rating.avatar">
                </div>
                <div class="time">{{rating.rateTime | formatDate}}</div>
                <p class="text">
                  <span :class="{'zan':rating.rateType===0}">赞</span>{{rating.text}}
                </p>

              </li>
            </ul>
            <div class="no-rating" v-show="!food.ratings || !food.ratings.length">
              暂无评价
            </div>
          </div>
        </div>
      </div>
    </transition>
  </div>
</template>

<script type="text/ecmascript-6">
  import {formatDate} from '../../common/js/date'
  import BScroll from 'better-scroll'
  import cartcontrol from '../cartcontrol/cartcontol'
  import Vue from 'vue'
  import split from '../split/split'
  import ratingSelect from '../ratingSelect/ratingselect'
  const ALL = 2
  export default{
    filters: {
      formatDate(time) {
        let date = new Date(time)
        return formatDate(date, 'yyyy-MM-dd hh:mm')
      }
    },
    methods: {
      ratingToggle(target) {
        this.onlyContent = target
        this.$nextTick(() => {
          this.scroll.refresh()
        })
      },
      ratingSelect(target) {
        this.selectType = target
        this.$nextTick(() => {
          this.scroll.refresh()
        })
      },
      needShow(type, text) {
        if (this.onlyContent && !text) {
          return false
        }
        if (this.selectType === ALL) {
          return true
        } else {
          return type === this.selectType
        }
      },
      show() {
        this.showFlag = true
        this.selectType = ALL
        this.onlyContent = true
        this.$nextTick(() => {
          if (!this.scroll) {
            this.scroll = new BScroll(this.$refs.foodN, {
              click: true
            })
          } else {
            this.scroll.refresh()
          }
        })
      },
      addFirst(event) {
        if (!event._constructed) {
          return
        }
        this.$emit('increment', event.target)
        Vue.set(this.food, 'count', 1)
      },
      hide() {
        this.showFlag = false
      }
    },
    props: {
      food: {
        type: Object
      }
    },
    data() {
      return {
        showFlag: false,
        selectType: ALL,
        onlyContent: true,
        desc: {
          all: '全部',
          positive: '推荐',
          negative: '吐槽'
        }

      }
    },
    components: {
      cartcontrol,
      split,
      ratingSelect
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .foodN
    position fixed
    left 0
    top 0
    z-index 30
    width 100%
    background #fff
    bottom 48px
    overflow hidden

  .food
    transition all .2s linear
    transform translate3d(0, 0, 0)
    &.move-enter, &.move-leave
      transform translate3d(100%, 0, 0)
    .image-header
      position relative
      width 100%
      height 0
      padding-top 100%
      img
        position absolute
        left 0
        top 0
        height 100%
        width 100%
      .back
        position absolute
        top 10px
        left 0
        display block
        padding 10px
        font-size 20px
        color #fff
    .content
      padding 18px
      position relative
      .title
        line-height 14px
        margin-bottom 8px
        font-size 14px
        font-weight 700
        color rgb(7, 17, 27)
      .detail1
        margin-bottom 18px
        height 10px
        line-height 10px
        font-size 0
        .sell-count, .rating
          font-size 10px
          color rgb(147, 152, 159)
        .sell-count
          margin-right 12px
      .price
        font-weight 700
        line-height 24px
        .now
          margin-right 8px
          font-size 14px
          color rgb(240, 20, 20)
        .old
          text-decoration line-through
          font-size 10px
          color rgb(147, 153, 159)
      .cartcontrol-wrapper
        position absolute
        right 12px
        bottom 12px
      .buy
        position absolute
        right 18px
        bottom 18px
        z-index 10
        height 24px
        line-height 24px
        padding 0 24px
        box-sizing border-box
        font-size 10px
        border-radius 12px
        color #fff
        background: rgb(0, 160, 220)
        opacity 1
        transition all .2s
        &.fade-enter, &.fade-leave
          opacity 0
    .info
      padding 18px
      .title
        line-height 24px
        font-size 14px
        margin-bottom 6px
        color rgb(7, 17, 27)
      .text
        line-height 24px
        padding 0 8px
        font-size 12px
        color rgb(77, 85, 93)
    .rating
      padding-top 18px
      .title
        line-height 24px
        font-size 14px
        margin-left 18px
        color rgb(7, 17, 27)
      .rating-wrapper
        padding 0 18px
        .rating-item
          position relative
          border-1px(rgba(7, 17, 27, .1))
          padding 16px 0
          .user
            position absolute
            right 0
            top 16px
            font-size 0
            line-height 12px
            .name
              display inline-block
              vertical-align top
              font-size 10px
              color rgb(147, 153, 159)
              margin-right 6px
            .avatar
              border-radius 50%
        .time
          margin-bottom 6px
          line-height 12px
          font-size 12px
          color rgb(147, 153, 159)
        .text
          line-height 16px
          font-size 12px
          color rgb(7, 17, 27)
          .zan, .unzan
            line-height 24px
            margin-right 4px
            color rgb(0, 160, 220)
          .unzan
            color rgb(147, 153, 159)
    .no-rating
      padding 16px 0
      font-size 12px
      color rgb(7, 17, 27)
</style>

