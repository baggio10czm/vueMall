<template>
  <div class="seller" ref="sellerWrapper">
    <div class="seller-content">
      <!--商家评价收藏配送-->
      <div class="overView">
        <h1 class="title">{{seller.name}}</h1>
        <div class="desc border-bottom1px">
          <star :size="36" :score="seller.score"></star>
          <div class="text">{{seller.ratingCount}}</div>
          <div class="text">月售{{seller.sellCount}}</div>
        </div>
        <ul class="remark">
          <li class="block">
            <h2>起送价</h2>
            <div class="content">
              <span class="stress">{{seller.minPrice}}</span>元
            </div>
          </li>
          <li class="block">
            <h2>商家配送</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryPrice}}</span>元
            </div>
          </li>
          <li class="block">
            <h2>平均配送时间</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryPrice}}</span>分钟
            </div>
          </li>
        </ul>
        <div class="favorite" @click="toggleFavorite">
          <span :class="['icon-favorite',{'active':favorite}]"></span>
          <span class="text">{{favoriteText}}</span>
        </div>
      </div>
      <!--商家评价收藏配送 END-->
      <split></split>
      <!--公告与活动-->
      <div class="bulletin">
        <h1 class="title">公告与活动</h1>
        <div class="content-wrapper border-bottom1px">
          <p class="content">{{seller.bulletin}}</p>
        </div>
      </div>
      <ul v-if="seller.supports" class="supports">
        <li class="support-item" v-for="(item,index) in seller.supports">
          <span :class="['icon',classNameArr[item.type]]"></span>
          <span class="text">{{item.description}}</span>
        </li>
      </ul>
      <!--公告与活动 END-->
      <split></split>
      <!--商家实景-->
      <div class="pics">
        <h1 class="title">商家实景</h1>
        <div class="pic-wrapper" ref="picWrapper">
          <ul class="pic-list" ref="picList">
            <li class="pic-item" v-for="pic in seller.pics">
              <img :src="pic" width="120" height="90" alt="">
            </li>
          </ul>
        </div>
      </div>
      <!--商家实景 END-->
      <split></split>
      <!--商家信息-->
      <div class="info">
        <h1 class="title">商家信息</h1>
        <ul>
          <li class="info-item" v-for="info in seller.infos">{{info}}</li>
        </ul>
      </div>
      <!--商家信息 END-->
    </div>
  </div>
</template>

<script>
  import BScroll from 'better-scroll'
  import star from 'components/star/star'
  import split from 'components/split/split'
  import {saveToLocal,loadFromLocal} from 'common/js/store'

  export default {
    name: "seller",
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        // 读取 缓存判断是否收藏
        favorite:(()=>{
          return loadFromLocal(this.seller.id,'favorite',false)
        })(),
        // 活动图片对应class
        classNameArr: ['decrease', 'discount', 'special', 'invoice', 'guarantee']
      }
    },
    mounted() {
      this.$nextTick(() => {
        if(!this.scroll){
          this.scroll = new BScroll(this.$refs.sellerWrapper, {
            click: true
          });
        }
        this.picsWidth();
      })
    },
    methods: {
      picsWidth() { // 根据商家实景图片 计算容器宽度
        if (this.seller.pics) {
          let picWidth = 120;
          let margin = 6;
          let width = (picWidth + margin) * this.seller.pics.length - margin;  // -margin 是因为最后一个图 没有  margin-right
          this.$refs.picList.style.width = width + 'px';

          this.$nextTick(() => {
            this.picScroll = new BScroll(this.$refs.picWrapper, {
              scrollX: true,
              eventPassthrough: 'vertical'   // 纵向的滚动还是保留原生滚动
            })
          })
        }
      },
      toggleFavorite(){
        this.favorite = !this.favorite;
        saveToLocal(this.seller.id,'favorite',this.favorite)
      }
    },
    computed:{
      favoriteText(){
        return this.favorite ? '已收藏':'收藏';
      }
    },
    components: {
      star,
      split
    }
  }
</script>

<style scoped>
  @import "../../common/css/icon.css";
  .seller {
    position: absolute;
    top: 174px;
    left: 0;
    bottom: 0;
    width: 100%;
    overflow: hidden;
  }

  .overView {
    position: relative;
    padding: 18px;
  }

  .overView .title {
    margin-bottom: 8px;
    line-height: 14px;
    color: rgb(7, 17, 27);
    font-size: 14px;
  }

  .overView .desc {
    position: relative;
    padding-bottom: 18px;
    line-height: 18px;
    font-size: 0;
  }

  .desc .star {
    display: inline-block;
    margin-right: 8px;
    vertical-align: top;
  }

  .desc .text {
    display: inline-block;
    line-height: 18px;
    font-size: 10px;
    margin-right: 12px;
    vertical-align: top;
    color: rgb(77, 85, 93);
  }

  .remark {
    display: flex;
    padding-top: 18px;
  }
  .favorite {
    position: absolute;
    width: 50px;
    right: 11px;
    top: 18px;
    text-align: center;
  }
  .icon-favorite{
    display: block;
    color: #d4d6d9;
    margin-bottom: 4px;
    line-height: 24px;
    font-size: 24px;
  }
  .icon-favorite.active{
    color: rgb(240,20,20);
  }
  .favorite .text {
    line-height: 10px;
    font-size: 10px;
    color: rgb(77, 85, 93);
  }
  .remark .block {
    flex: 1;
    text-align: center;
    border-right: 1px solid rgba(7, 17, 27, 0.1);
  }

  .remark .block:last-child {
    border-right: 0;
  }

  .remark .block h2 {
    margin-bottom: 4px;
    line-height: 10px;
    font-size: 10px;
    color: rgb(147, 153, 159);
  }

  .content {
    font-size: 10px;
    line-height: 24px;
    color: rgb(7, 17, 27);
  }

  .content .stress {
    font-size: 24px;
  }

  .bulletin {
    padding: 18px 18px 0 18px;
  }

  .bulletin .title {
    margin-bottom: 8px;
    line-height: 14px;
    color: rgb(7, 17, 27);
    font-size: 14px;
  }

  .bulletin .content-wrapper {
    position: relative;
    padding: 0 12px 16px 12px;
  }

  .bulletin .content-wrapper .content {
    line-height: 24px;
    font-size: 12px;
    color: rgb(240, 20, 20);
  }

  .supports {
    width: 80%;
    padding: 25px 15px;
  }

  .supports .support-item {
    padding: 0 12px;
    margin-bottom: 15px;
    font-size: 0;
  }

  .supports .support-item:last-child {
    margin-bottom: 0;
  }

  .supports .support-item .icon {
    display: inline-block;
    width: 16px;
    height: 16px;
    vertical-align: top;
    margin-right: 6px;
    background-size: 16px 16px;
    background-repeat: no-repeat;
  }

  .supports .support-item .icon.decrease {
    background-image: url('decrease_2@2x.png');
  }

  .supports .support-item .icon.discount {
    background-image: url('discount_2@2x.png');
  }

  .supports .support-item .icon.guarantee {
    background-image: url('guarantee_2@2x.png');
  }

  .supports .support-item .icon.invoice {
    background-image: url('invoice_2@2x.png');
  }

  .supports .support-item .icon.special {
    background-image: url('special_2@2x.png');
  }

  .supports .support-item .text {
    font-size: 12px;
    line-height: 16px;
  }

  .pics {
    padding: 18px;
  }

  .pics .title {
    margin-bottom: 12px;
    line-height: 14px;
    color: rgb(7, 17, 27);
    font-size: 14px;
  }

  .pics .pic-wrapper {
    width: 100%;
    overflow: hidden;
    white-space: nowrap;
  }

  .pics .pic-lsit {
    font-size: 0;
  }

  .pics .pic-item {
    display: inline-block;
    margin-right: 6px;
    width: 120px;
    height: 90px;
  }

  .pics .pic-item:last-child {
    margin-right: 0;
  }
  .info {
    color: rgb(7,17,27);
    padding: 18px 18px 0 18px;
  }
  .info .title {
    line-height: 14px;
    padding-bottom: 12px;
    font-size: 14px;
  }
  .info .info-item{
    padding: 16px 12px;
    line-height: 16px;
    font-size: 12px;
  }
</style>
