<template>
  <transition name="food-ani">
    <div v-show="showFlag" class="food" ref="foodWrapper">
      <div class="food-content">
        <div class="image-header">
          <img :src="food.image" alt="">
          <div class="back-wrapper" @click="hide">
            <i class="icon-arrow_lift"></i>
          </div>
        </div>
        <div class="content">
          <h1 class="title">{{food.name}}</h1>
          <div class="detail">
            <span class="sell-count">月售{{food.sellCount}}份</span>
            <span class="rating">好评率{{food.rating}}%</span>
          </div>
          <div class="price">
            <span class="now">￥{{food.price}}</span>
            <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
          </div>
          <div class="cartControl-wrapper">
            <cartControl :food="food" @addCart="addCart"></cartControl>
          </div>
          <transition name="buy-ani">
            <div class="buy" v-show="!food.count|| food.count === 0" @click.stop="addFirst($event)">加入购物车</div>
          </transition>
        </div>
        <split v-if="food.info"></split>
        <div class="info" v-if="food.info">
          <h1 class="title">商品信息</h1>
          <p class="text">{{food.info}}</p>
        </div>
        <split></split>
        <div class="rating">
          <h1 class="title">商品评价</h1>
          <ratingSelect :selectType="selectType" :onlyContent="onlyContent" :desc="desc"
                        :ratings="food.ratings"></ratingSelect>
          <div class="rating-wrapper">
            <ul v-show="food.ratings && food.ratings.length">
              <li v-show="needShow(rating.rateType,rating.text)" v-for="rating in food.ratings"
                  class="rating-item border-bottom1px">
                <div class="user">
                  <span class="name">{{rating.username}}</span>
                  <img :src="rating.avatar" alt="" width="12" height="12" class="avatar">
                </div>
                <div class="time">{{rating.rateTime | formatDate}}</div>
                <p class="text">
                  <span :class="[rating.rateType === 0 ? 'icon-thumb_up':'icon-thumb_down']"></span>{{rating.text}}
                </p>
              </li>
            </ul>
            <div class="no-rating" v-show="!food.ratings || !food.ratings.length">毫无评价可言！</div>
          </div>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
  import BScroll from 'better-scroll'
  import cartControl from 'components/cartcontrol/cartcontrol'
  import split from 'components/split/split'
  import ratingSelect from 'components/ratingSelect/ratingSelect'
  import {formatDate} from 'common/js/date'
  import {ALL, NEGATUVE, POSITIVE} from "config/config"

  export default {
    name: "food",
    props: {
      food: {
        type: Object
      }
    },
    data() {
      return {
        showFlag: false,
        selectType: ALL,
        onlyContent: false,
        desc: {
          all: '全部',
          positive: '推荐',
          negative: '吐槽'
        },
        ratingsData: []
      }
    },
    filters:{
      formatDate(time) {
        let date = new Date(time);
        return formatDate(date, 'yyyy-MM-dd hh:mm');
      }
    },
    methods: {
      show() {
        this.showFlag = true;
        this.selectType = ALL;
        this.onlyContent = true;
        if (!this.scroll) {
          this.scroll = new BScroll(this.$refs.foodWrapper, {
            click: true
          })
        }
      },
      hide() {
        this.showFlag = false;
      },
      addFirst(event) {
        this.$set(this.food, 'count', 1);
        this.$emit('addCart', event.target);
      },
      addCart(el) {
        this.$emit('addCart', el);
      },
      needShow(rateType, text) {
        if (this.onlyContent && !text) {
          return false;
        }
        if (this.selectType === ALL) {
          return true;
        } else {
          return this.selectType === rateType;
        }
      },
      // foodDataFn() {
      //   console.log(this.food.ratings);
      //   let dataArr = [];
      //   if (this.selectType === 2) {
      //     dataArr = this.food.ratings
      //   } else {
      //     dataArr = this.food.ratings.filter((rating) => {
      //       return rating.rateType === this.selectType;
      //     })
      //   }
      //   if (this.onlyContent) {
      //     dataArr = dataArr.filter((data) => {
      //       return data.text !== "";
      //     })
      //   }
      //   this.ratingsData = dataArr;
      // }
    },
    // watch: {
    //   selectType: function (newVal, oldVal) {
    //     this.foodDataFn();
    //   },
    //   onlyContent: function (newVal, oldVal) {
    //     this.foodDataFn();
    //   },
    // },
    components: {
      cartControl,
      split,
      ratingSelect
    }
  }
</script>

<style scoped>
  @import "../../common/css/icon.css";

  .food {
    position: fixed;
    top: 0;
    left: 0;
    bottom: 48px;
    z-index: 30;
    width: 100%;
    background-color: #fff;
  }

  .image-header {
    position: relative;
    width: 100%;
    height: 0;
    padding-top: 100%;
  }

  .image-header img {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
  }

  .back-wrapper {
    position: absolute;
    top: 10px;
    left: 0;
  }

  .back-wrapper .icon-arrow_lift {
    display: block;
    padding: 10px;
    font-size: 20px;
    color: #fff;
  }

  .content {
    position: relative;
    padding: 18px;
  }

  .content .title {
    line-height: 14px;
    margin-bottom: 8px;
    font-size: 14px;
    font-weight: 700;
    color: rgb(7, 17, 27);
  }

  .content .detail {
    margin-bottom: 18px;
    height: 10px;
    line-height: 10px;
    font-size: 0;
  }

  .content .detail .sell-count, .content .detail .rating {
    font-size: 10px;
    color: rgb(147, 153, 159);
  }

  .content .detail .sell-count {
    margin-right: 12px;
  }

  .content .price {
    font-weight: 700;
    line-height: 24px;
  }

  .content .price .now {
    margin-right: 8px;
    font-size: 14px;
    color: rgb(240, 20, 20);
  }

  .content .price .old {
    margin-right: 8px;
    font-size: 10px;
    color: rgb(147, 153, 159);
  }

  .content .cartControl-wrapper {
    position: absolute;
    right: 12px;
    bottom: 12px;
  }

  .content .buy {
    position: absolute;
    right: 18px;
    bottom: 18px;
    z-index: 10;
    height: 24px;
    line-height: 24px;
    padding: 0 12px;
    box-sizing: border-box;
    font-size: 10px;
    border-radius: 12px;
    background-color: rgb(0, 160, 220);
    color: #fff;
  }

  .food-content .info {
    padding: 18px;
  }

  .food-content .info .title {
    line-height: 14px;
    margin-bottom: 6px;
    font-size: 14px;
    color: rgb(7, 17, 27);
  }

  .food-content .info .text {
    line-height: 24px;
    padding: 0 8px;
    font-size: 12px;
    color: rgb(77, 85, 93)
  }

  .rating {
    padding-top: 18px;
  }

  .rating .title {
    line-height: 14px;
    margin-left: 18px;
    font-size: 14px;
    color: rgb(7, 17, 27);
  }

  .rating-wrapper {
    padding: 0 18px;
  }

  .rating-wrapper ul .rating-item {
    position: relative;
    padding: 16px 0;
  }

  .rating-wrapper ul .rating-item .user {
    position: absolute;
    right: 0;
    top: 16px;
    font-size: 0;
    line-height: 12px;
  }

  .rating-wrapper ul .rating-item .user .name {
    display: inline-block;
    margin-right: 6px;
    vertical-align: top;
    font-size: 10px;
    color: rgb(147, 153, 159);
  }

  .rating-wrapper ul .rating-item .user .avatar {
    border-radius: 50%;
  }

  ul .rating-item .time {
    margin-bottom: 6px;
    font-size: 10px;
    line-height: 12px;
    color: rgb(147, 153, 159);
  }

  ul .rating-item .text {
    line-height: 16px;
    font-size: 12px;
    color: rgb(7, 17, 27);
  }

  .no-rating {
    padding: 16px 0;
    font-size: 12px;
    color: rgb(147, 153, 159);
  }

  .icon-thumb_up, .icon-thumb_down {
    margin-right: 4px;
    line-height: 16px;
    font-size: 12px;
  }

  .icon-thumb_up {
    color: rgb(0, 160, 220);
  }

  .icon-thumb_down {
    color: rgb(147, 153, 159);
  }

  .buy-ani-enter, .buy-ani-leave-to {
    opacity: 0;
  }

  .buy-ani-enter-active, .buy-ani-leave-active {
    transition: all .7s ease;
  }

  .food-ani-enter, .food-ani-leave-to {
    transform: translateX(100%);
  }

  .food-ani-enter-active, .food-ani-leave-active {
    transition: all .7s ease;
  }
</style>
