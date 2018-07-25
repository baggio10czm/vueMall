<template>
  <div class="ratings" ref='ratingsWrapper'>
    <div class="ratings-content">
      <!-- 评价信息 -->
      <div class="overView">
        <div class="overView-left">
          <h1 class="score">{{seller.score}}</h1>
          <div class="title">综合评分</div>
          <div class="rank">高于周边商家{{seller.rankRate}}%</div>
        </div>
        <div class="overView-right">
          <div class="score-wrapper">
            <div class="title">服务态度</div>
            <star :size='36' :score="seller.serviceScore"></star>
            <span class="score">{{seller.serviceScore}}</span>
          </div>
          <div class="score-wrapper">
            <div class="title">商品评分</div>
            <star :size='36' :score="seller.foodScore"></star>
            <span class="score">{{seller.foodScore}}</span>
          </div>
          <div class="delivery-wrapper">
            <span class="title">送达时间</span>
            <span class="delivery">{{seller.deliveryTime}}分钟</span>
          </div>
        </div>
      </div>
      <!-- 评价信息 END-->
      <split></split>
      <!-- 评价过滤 -->
      <ratingSelect :selectType="selectType" :onlyContent="onlyContent" :desc="desc" :ratings="ratings"></ratingSelect>
      <!-- 评价过滤 END-->
      <!--评价内容列表-->
      <div class="rating-wrapper">
        <ul>
          <li v-for="rating in ratings" v-show="needShow(rating.rateType,rating.text)" class="rating-item border-bottom1px">
            <!--用户头像-->
            <div class="avatar">
              <img width="28" height="28" :src="rating.avatar" alt="">
            </div>
            <!--用户头像 END-->
            <div class="content">
              <!--用户名-->
              <h1 class="name">{{rating.username}}</h1>
              <!--评价 以及 送达时间 -->
              <div class="star-wrapper">
                <star :size="24" :score="rating.score"></star>
                <span class="delivery" v-show="rating.deliveryTime">{{rating.deliveryTime}}分钟送达</span>
              </div>
              <!--评价内容 -->
              <p class="text">{{rating.text}}</p>
              <!--推荐内容 -->
              <div class="recommend" v-show="rating.recommend.length">
                <i class="icon-thumb_up"></i>
                <span v-for="item in rating.recommend" class="item">{{item}}</span>
              </div>
              <!--评论时间 -->
              <div class="time">{{rating.rateTime | formatDate}}</div>
            </div>
          </li>
        </ul>
      </div>
      <!--评价内容列表 END-->
    </div>
  </div>
</template>

<script>
  import BScroll from 'better-scroll'
  import {ERR_OK} from 'config/config.js'
  import axios from 'axios'
  import star from 'components/star/star'
  import split from 'components/split/split'
  import ratingSelect from 'components/ratingSelect/ratingSelect'
  import {formatDate} from 'common/js/date'
  import {ALL, NEGATUVE, POSITIVE} from "config/config"

  export default {
    name: "ratings",
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        ratings: [],
        selectType: ALL,
        onlyContent: false,
        desc: {
          all: '全部',
          positive: '满意',
          negative: '不满意'
        }
      }
    },
    filters: {
      formatDate(time) {
        let date = new Date(time);
        return formatDate(date, 'yyyy-MM-dd hh:mm');
      }
    },
    created() {
      axios.get('api/ratings').then((res) => {
        if (res.data.errno === ERR_OK) {
          this.ratings = res.data.data;
          this.$nextTick(() => {
            this.scroll = new BScroll(this.$refs.ratingsWrapper, {
              click: true
            })
          })
        }
      })
    },
    methods:{
      // 传入 评价类型,和值
      needShow(rateType,text){
        // 当是只显示有文字的评论 以及 值为空的时候 返回 false 不显示当前项
        if (this.onlyContent && !text) {
          return false;
        }
        // 评价类型为全部时 返回 true 全部显示
        if (this.selectType === ALL) {
          return true;
        } else {  // 否则,只显示传入的类型
          return this.selectType === rateType;
        }
      }
    },
    components: {
      star,
      split,
      ratingSelect
    }
  }
</script>

<style scoped>
  @import "../../common/css/icon.css";
  .ratings {
    position: absolute;
    top: 174px;
    left: 0;
    bottom: 0;
    width: 100%;
    overflow: hidden;
  }

  .overView {
    display: flex;
    padding: 18px 0;
  }

  .overView-left {
    flex: 0 0 137;
    width: 137px;
    border-right: 1px solid rgba(7, 17, 27, .1);
    text-align: center;
    padding: 6px 0;
  }

  .overView-left .score {
    margin-bottom: 6px;
    line-height: 28px;
    font-size: 24px;
    color: rgb(255, 153, 0);
  }

  .overView-left .title {
    margin-bottom: 8px;
    line-height: 12px;
    font-size: 12px;
    color: rgb(7, 17, 27);
  }

  .overView-left .rank {
    line-height: 10px;
    font-size: 10px;
    color: rgb(147, 153, 159);
  }

  .overView-right {
    flex: 1;
    padding: 6px 0 6px 24px;
  }

  .overView-right .score-wrapper {
    margin-bottom: 8px;
    font-size: 0;
  }

  .overView-right .score-wrapper .title {
    display: inline-block;
    line-height: 18px;
    vertical-align: top;
    font-size: 12px;
    color: rgb(7, 17, 27);
  }

  .overView-right .score-wrapper .star {
    display: inline-block;
    vertical-align: top;
    margin: 0 12px;
  }

  .overView-right .score-wrapper .score {
    display: inline-block;
    vertical-align: top;
    line-height: 18px;
    font-size: 12px;
    color: rgb(255, 153, 0);
  }

  .overView-right .delivery-wrapper {
    font-size: 0;
  }

  .overView-right .delivery-wrapper .title {
    vertical-align: top;
    font-size: 12px;
    color: rgb(7, 17, 27);
  }

  .overView-right .delivery-wrapper .title {
    font-size: 12px;
    color: rgb(7, 17, 27);
  }

  .overView-right .delivery-wrapper .delivery {
    margin: 0 12px;
    font-size: 12px;
    color: rgb(147, 153, 159);
  }

  @media (max-width: 360px) {
    .overView-left {
      flex: 0 0 125px;
      width: 125px;
    }

    .overView-right {
      padding-left: 15px;
    }
  }

  @media (max-width: 340px) {
    .overView-left {
      flex: 0 0 115px;
      width: 115px;
    }

    .overView-right {
      padding-left: 6px;
    }
  }
  .rating-wrapper {
    padding: 0 18px;
  }
  .rating-item {
    display: flex;
    position: relative;
    padding: 12px 0;
  }
  .rating-item .avatar {
    flex: 0 0 28px;
    width: 28px;
    margin-right: 12px;
  }
  .rating-item .avatar img {
    border-radius: 50%;
  }
  .rating-item  .content{
    position: relative;
    flex: 1;
  }
  .rating-item .content .name{
    line-height: 12px;
    font-size: 12px;
    margin-bottom: 4px;
    color:rgb(7,17,27);
  }
  .rating-item .content .star-wrapper {
    margin-bottom: 6px;
    font-size: 0;
  }
  .rating-item .content .star-wrapper .star {
    display: inline-block;
    margin-right: 6px;
    vertical-align: top;
  }
  .rating-item .content .star-wrapper .delivery {
    display: inline-block;
    vertical-align: top;
    font-size: 10px;
    color: rgb(147, 153, 159);
  }
  .rating-item .content .text {
    line-height: 18px;
    font-size: 12px;
    margin-bottom: 8px;
  }
  .rating-item .recommend {
    line-height: 16px;
    font-size: 0;
  }
  .rating-item .recommend .icon-thumb_up,.rating-item .recommend .item{
    display: inline-block;
    margin: 0 8px 4px 0;
    font-size: 9px;
  }
  .rating-item .recommend .icon-thumb_up {
    color: rgb(0, 160, 220);
  }
  .rating-item .recommend .item {
    padding: 0 6px;
    border: 1px solid rgba(7,17,27,.1);
    color: rgb(147, 153, 159);
    border-radius: 2px;
    background-color: #fff;
  }
  .rating-item .time {
    position: absolute;
    top: 0;
    right: 0;
    font-size: 10px;
    color: rgb(147, 153, 159);
  }
</style>
