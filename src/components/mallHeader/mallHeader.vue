<template>
  <div class="header">
    <div class="content-wrapper" v-if="seller">
      <div class="avatar">
        <img width="64" height="64" :src="seller.avatar" alt="">
      </div>
      <div class="content">
        <div class="title">
          <span class="brand"></span>
          <span class="name">{{seller.name}}</span>
        </div>
        <div class="description">
          {{seller.description}} / {{seller.deliveryTime}}分钟送达
        </div>
        <div v-if="seller.supports" class="support">
          <span :class="['icon',classNameArr[seller.supports[0].type]]"></span>
          <span class="text">{{seller.supports[0].description}}</span>
        </div>
      </div>
      <div v-if="seller.supports" class="supports-count" @click="showDetail">
        <span class="count ceshi">{{seller.supports.length}}个</span>
        <span class="icon-keyboard_arrow_right"></span>
      </div>
    </div>
    <div class="bulletin-wrapper" @click="showDetail">
      <span class="bulletin-title"></span><span class="bulletin-text">{{seller.bulletin}}</span>
      <i class="icon-keyboard_arrow_right"></i>
    </div>
    <div class="background">
      <img :src="seller.avatar" alt="" width="100%" height="100%" >
    </div>
    <transition name="detail-ani">
      <div class="detail" v-show="detailShow">
        <div class="detail-wrapper clearfix">
          <div class="detail-main">
            <h2>{{seller.name}}</h2>
            <div class="star-wrapper">
              <star :size="36" :score="seller.score"></star>
            </div>
            <div class="textTitle">
              <div class="line"></div>
              <div class="text">优惠信息</div>
              <div class="line"></div>
            </div>
            <ul v-if="seller.supports" class="supports">
              <li class="support-item" v-for="(item,index) in seller.supports">
                <span :class="['icon',classNameArr[item.type]]"></span>
                <span class="text">{{item.description}}</span>
              </li>
            </ul>
            <div class="textTitle">
              <div class="line"></div>
              <div class="text">商家公告</div>
              <div class="line"></div>
            </div>
            <div class="bulletin">
              <p class="content">{{seller.bulletin}}</p>
            </div>
          </div>
        </div>
        <div class="detail-close" @click="hideDetail">
          <i class="icon-close"></i>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
  import star from 'components/star/star.vue'
  export default {
    name: "mallHeader",
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        classNameArr: ['decrease', 'discount', 'special', 'invoice', 'guarantee'],
        detailShow:false
      }
    },
    methods:{
      showDetail(){
        this.detailShow =true;
      },
      hideDetail(){
        this.detailShow =false;
      }
    },
    components:{
      star
    }
  }
</script>

<style scoped>
  @import "../../common/css/icon.css";
  .header {
    color: #fff;
    overflow: hidden;
    position: relative;
    background-color: rgba(7,17,27,.4);
  }

  .content-wrapper {
    font-size: 0;
    padding: 24px 12px 18px 24px;
    position: relative;
  }

  .avatar {
    display: inline-block;
    vertical-align: top;
  }

  .avatar img {
    border-radius: 2px;
  }

  .content {
    display: inline-block;
    margin-left: 16px;
  }

  .content .title {
    margin: 2px 0 8px 0;
  }

  .content .title .brand {
    display: inline-block;
    vertical-align: top;
    width: 30px;
    height: 18px;
    background: url("brand@2x.png") no-repeat;
    background-size: 30px 18px;
  }

  .content .title .name {
    display: inline-block;
    margin-left: 6px;
    font-size: 16px;
    height: 16px;
    line-height: 18px;
    font-weight: bold;
  }

  .supports-count {
    position: absolute;
    right: 12px;
    bottom: 18px;
    padding: 0 8px;
    height: 24px;
    line-height: 24px;
    border-radius: 4px;
    background-color: rgba(0, 0, 0, .2);
    text-align: center;
  }

   .supports-count .count {
    font-size: 10px;
    vertical-align: top;
    line-height: 24px;
  }

  .supports-count .icon-keyboard_arrow_right {
    margin-left: 2px;
    font-size: 10px;
    height: 26px;
    line-height: 26px;
  }

  .description {
    margin-bottom: 10px;
    line-height: 12px;
    font-size: 12px;
  }

  .support .icon {
    display: inline-block;
    width: 12px;
    height: 12px;
    vertical-align: top;
    margin-right: 4px;
    background-size: 12px 12px;
    background-repeat: no-repeat;
  }

  .support .text {
    line-height: 12px;
    font-size: 10px;
  }

  .support .icon.decrease {
    background-image: url('decrease_1@2x.png');
  }

  .support .icon.discount {
    background-image: url('discount_1@2x.png');
  }

  .support .icon.guarantee {
    background-image: url('guarantee_1@2x.png');
  }

  .support .icon.invoice {
    background-image: url('invoice_1@2x.png');
  }

  .support .icon.special {
    background-image: url('special_1@2x.png');
  }
  .bulletin-wrapper {
    position: relative;
    height: 28px;
    line-height: 30px;
    padding: 0 22px 0 12px;
    background-color: rgba(7,17,27,.2);
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }
  .bulletin-title {
    display: inline-block;
    vertical-align: top;
    margin-top: 8px;
    width: 22px;
    height: 12px;
    background: url('bulletin@2x.png') no-repeat;
    background-size: 22px 12px;
  }
  .bulletin-text {
    vertical-align: top;
    margin: 0 4px;
    font-size: 10px;
  }
  .bulletin-wrapper .icon-keyboard_arrow_right {
    position: absolute;
    font-size: 10px;
    right: 12px;
    top: 8px;
  }
  .background {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: -1;
    filter: blur(10px);
  }
  .detail {
    position: fixed;
    top: 0;
    left: 0;
    z-index: 100;
    width: 100%;
    height: 100%;
    overflow: auto;
    background-color: rgba(7,17,27,.9);
   backdrop-filter:blur(10px);  /* 苹果手机背景模糊 */
  }
  .detail-wrapper{
    width: 100%;
    min-height: 100%;
  }
  .detail-main {
    margin-top: 64px;
    padding-bottom: 64px;
  }
  .star-wrapper {
    margin-top: 18px;
    padding: 2px 0;
    text-align: center;
  }
  .textTitle {
    display: flex;
    width: 80%;
    margin: 28px auto 24px;
  }
  .textTitle .line {
    flex: 1;
    position: relative;
    top: -6px;
    border-bottom: 1px solid rgba(255,255,255,.2);
  }
  .textTitle .text {
    padding: 0 12px;
    font-weight: 700;
    font-size: 14px;
  }
  .supports{
    width: 80%;
    margin: 0 auto;
  }
  .supports .support-item {
    padding: 0 12px;
    margin-bottom: 12px;
    font-size: 0;
  }
  .supports .support-item:last-child{
    margin-bottom: 0;
  }
  .supports .support-item .icon{
    display: inline-block;
    width: 16px;
    height: 16px;
    vertical-align: top;
    margin-right: 6px;
    background-size:16px 16px;
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
  .bulletin {
    width: 80%;
    margin: 0 auto;
    line-height: 24px;
    font-size: 12px;
  }
  .detail-main h2 {
    line-height: 16px;
    text-align: center;
    font-size: 16px;
    font-weight: 700;
  }
  .detail-close{
    position: relative;
    width: 32px;
    height: 32px;
    margin:-47px auto 0 ;
    clear: both;
    font-size: 32px;
  }
  .detail-ani-enter,.detail-ani-leave-to{
    opacity: 0;
  }
  .detail-ani-enter-active,.detail-ani-leave-active{
    transition: all .9s ease;
  }
</style>
