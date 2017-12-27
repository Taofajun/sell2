<template>
  <div class="ratings" ref="rating">
    <div class="ratings-content">
      <div class="overview">
        <div class="overview-left">
          <h1 class="score">
            {{seller.score}}
          </h1>
          <div class="title">
            综合评分
          </div>
          <div class="rank">
            高于周边商家{{seller.rankRate}}%
          </div>
        </div>
        <div class="overview-right">
          <div class="score-wrapper">
            <span class="title">服务态度</span>
            <star :size="36" :score="seller.serviceScore"></star>
            <span class="score">
              {{seller.serviceScore}}
            </span>
          </div>
          <div class="score-wrapper">
            <span class="title">商品评分</span>
            <star :size="36" :score="seller.foodScore"></star>
            <span class="score">
              {{seller.foodScore}}
            </span>
          </div>
          <div class="delivery-wrapper">
            <span class="title">送达时间</span>
            <span class="delivery">
              {{seller.deliveryTime}}分钟
            </span>
          </div>
        </div>
      </div>
      <split></split>
      <ratingselect @select="ratingSelect" :select-type="selectType" :only-content="onlyContent"
                    :ratings="ratings"></ratingselect>
      <div class="rating-wrapper">
        <ul>
          <li v-for="rating in ratings" v-show="needShow(rating.rateType,rating.text)" class='rating-item'>
            <div class="avatar">
              <img width="28" height="28" :src="rating.avatar" alt="">
            </div>
            <div class="content">
              <h1 class="name">
                {{rating.username}}
              </h1>
              <div class="star-wrapper">
                <star :size="24" :score="rating.score"></star>
                <span class="delivery" v-show="rating.deliveryTime">
                </span>
              </div>
              <p class="text">{{rating.text}}</p>
              <div class="recommend" v-show="rating.recommend && rating.recommend.length">
                 <span v-for="item in rating.recommend">
                         {{item.recommend}}
                 </span>
              </div>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import {formatDate} from '../../common/js/date'
  import star from '../star/star'
  import split from '../split/split'
  import BScroll from 'better-scroll'
  import ratingselect from '../ratingSelect/ratingselect'
  const ALL = 2
  const ERROR = 0
  export default{
    data() {
      return {
        ratings: [],
        showFlag: false,
        selectType: ALL,
        onlyContent: true
      }
    },
    methods: {
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
      ratingSelect(target) {
        this.selectType = target
        this.$nextTick(() => {
          this.scroll.refresh()
        })
      }
    },
    created() {
      this.$http.get('/api/ratings').then((response) => {
        response = response.body
        if (response.errno === ERROR) {
          this.ratings = response.data
          this.$nextTick(() => {
            this.scroll = new BScroll(this.$refs.rating, {
              click: true
            })
          })
        }
      })
    },
    filters: {
      formatDate(time) {
        let date = new Date(time)
        return formatDate(date, 'yyyy-MM-dd hh:mm')
      }
    },
    props: {
      seller: {
        type: Object
      }
    },
    components: {
      star,
      split,
      ratingselect
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .ratings
    position absolute
    top 174px
    bottom 0
    width 100%
    left: 0
    overflow hidden
    .overview
      display flex
      padding 18px
      .overview-left
        flex 0 0 137px
        width 137px
        border-right 1px solid rgba(7, 17, 27, .1)
        text-align center
        padding 6px 0
        @media only screen and ( max-width: 320px)
          flex 0 0 120px
          width 120px
        .score
          line-height 28px
          font-size 24px
          color rgb(255, 153, 0)
          margin-bottom 6px
        .title
          line-height 12px
          font-size 12px
          color rgb(7, 17, 27)
          margin-bottom 8px
        .rank
          font-size 10px
          color rgb(147, 153, 159)
          line-height 10px
      .overview-right
        flex 1
        padding-left 24px
        padding 6px 0 6px 24px
        @media only screen and ( max-width: 320px)
          padding-left 6px
        .score-wrapper
          margin-bottom 8px
          font-size 0
          .title
            display inline-block
            vertical-align top
            font-size 12px
            color rgb(7, 17, 27)
            line-height 18px
          .star
            line-height 18px
            display inline-block
            vertical-align top
            margin 0 12px
          .score
            color rgb(255, 153, 0)
            font-size 12px
            display inline-block
            vertical-align top
        .delivery-wrapper
          font-size 0
          .title
            display inline-block
            vertical-align top
            font-size 12px
            color rgb(7, 17, 27)
            line-height 18px
          .delivery
            font-size 12px
            color rgb(147, 153, 159)
            margin-left 12px
    .rating-wrapper
      padding 0 18px
    .rating-item
      display flex
      padding 18px 0
      border-1px(rgba(7, 17, 27, .1))
      .avatar
        flex 0 0 28px
        width 28px
        margin-right 12px
        img
          border-radius 50%
      .content
        position relative
        flex 1
        .name
          line-height 12px
          font-size 10px
          color rgb(7, 17, 27)
          margin-bottom 4px
        .star-wrapper
          margin-bottom 6px
          font-size 0
          .star
            margin-right 6px
            display inline-block
            vertical-align middle
          .delivery
            display inline-block
            line-height 12px
            font-size 10px
            color rgb(147, 153, 159)
        .text
          line-height 18px
          font-size 12px
          margin-bottom 8px
        .recommend
          line-height 16px
</style>

