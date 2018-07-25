<template>
  <div class="shopcart" @click="toggleList">
    <div class="content">
      <div class="content-left">
        <div class="logo-wrapper">
          <!-- 当选择商品总数大于0时候 套用 highLight 类-->
          <div :class="['logo',{'highLight':totalCount>0}]">
            <i class="icon-shopping_cart"></i>
          </div>
          <!--购物车上的商品数量小图标-->
          <div class="num" v-show="totalCount>0">{{totalCount}}</div>
        </div>
        <div :class="['price',{'highLight':totalPrice>0}]">￥{{totalPrice}}</div>
        <div class="desc">另需配送费￥{{deliveryPrice}}</div>
      </div>
      <div class="content-right" @click.stop="pay">
        <div :class="['pay',payClass]">
          {{payDesc}}
        </div>
      </div>
    </div>
    <!--动画小球-->
    <div class="ball-container">
      <div v-for="ball in balls">
        <transition @before-enter="beforeEnter" @enter="enter" @after-enter="afterEnter">
          <div class="ball" v-show="ball.show">
            <div class="inner inner-hook"></div>
          </div>
        </transition>
      </div>
    </div>
    <!--动画小球 END -->
    <!--购物车商品列表-->
    <transition  name="list-ani">
      <div class="shopcart-list" v-show="listShow" @click.stop>
        <div class="list-header">
          <h1 class="title">购物车</h1>
          <span class="empty" @click="empty">清空</span>
        </div>
        <div class="list-content" ref="listContent">
          <ul>
            <li class="food border-bottom1px" v-for="food in selectFoods">
              <span class="name">{{food.name}}</span>
              <div class="price">
                <span>￥{{food.price * food.count}}</span>
              </div>
              <div class="cartcontrol-warpper">
                <cartControl :food="food"></cartControl>
              </div>
            </li>
          </ul>
        </div>
      </div>
    </transition>
    <!--购物车商品列表 END-->
    <!--购物车商品列表 遮罩-->
    <transition name="mask-ani">
      <div class="list-mask" v-show="listShow" @click.stop="hideList"></div>
    </transition>
    <!--购物车商品列表 遮罩 END-->
  </div>
</template>

<script>
  import cartControl from 'components/cartcontrol/cartcontrol'
  import BScroll from 'better-scroll'
  export default {
    name: "shopCart",
    data(){
      return{
        payClass:'no-enough',
        balls:[
          {
            show:false
          },
          {
            show:false
          },
          {
            show:false
          },
          {
            show:false
          },
          {
            show:false
          }
        ],
        dropBalls:[],
        fold:true
      }
    },
    props: {
      // 选择的所有商品
      selectFoods:{
        type:Array
      },
      deliveryPrice:{
        type:Number
      },
      minPrice:{
        type:Number
      }
    },
    computed:{
      // 计算所有选择商品总价
      totalPrice(){
        let total = 0;
        this.selectFoods.forEach((food)=>{
          total += food.price * food.count
        });
        return total;
      },
      // 计算所有选择商品个数
      totalCount(){
        let count = 0;
        this.selectFoods.forEach((food)=>{
          count += food.count
        });
        return count;
      },
      // 支付提示
      payDesc(){
        // 当总价为0的时候
        if(this.totalPrice === 0){
          this.payClass = 'no-enough';
          return `￥${this.minPrice}元起送`
        }else if (this.totalPrice < this.minPrice){   // 当总价小于最小配送额
          this.payClass = 'no-enough';
          return `还差￥${this.minPrice - this.totalPrice}元起送`
        }else{  // 当大于配送额
          this.payClass = 'enough';
          return '去结算';
        }
      },
      listShow() {
        if (!this.totalCount) {
          this.fold = true;
          return false;
        }
        if(!this.fold){
          if(!this.scroll){
            this.scroll = new BScroll(this.$refs.listContent,{
              click:true
            })
          }else {
            this.scroll.refresh();
          }
        }
        return !this.fold;
      }
    },
    methods:{
      drop(el){
        for (let i = 0; i < this.balls.length; i++) {
          let ball = this.balls[i];
          if(!ball.show){
            this.balls[i].show = true;
            ball.el = el;
            this.dropBalls.push(ball);
            return;
          }
        }
      },
      beforeEnter(el){
        let count = this.balls.length;
        while (count--) {
          let ball = this.balls[count];
          if (ball.show) {
            let rect = ball.el.getBoundingClientRect(); //获取小球的相对于视口的位移(小球高度)
            let x = rect.left - 32;
            let y = -(window.innerHeight - rect.top - 22); //负数,因为是从左上角往下的的方向
            el.style.display = ''; //清空display
            el.style.webkitTransform = `translate3d(0,${y}px,0)`;
            el.style.transform = `translate3d(0,${y}px,0)`;
            //处理内层动画
            let inner = el.getElementsByClassName('inner-hook')[0]; //使用inner-hook类来单纯被js操作
            inner.style.webkitTransform = `translate3d(${x}px,0,0)`;
            inner.style.transform = `translate3d(${x}px,0,0)`;
          }
        }
      },
      enter(el,done){
        el.offsetHeight; //触发重绘html
        this.$nextTick(() => { //让动画效果异步执行,提高性能
          el.style.display = '';
          el.style.webkitTransform = 'translate3d(0,0,0)';
          el.style.transform = 'translate3d(0,0,0)';
          //处理内层动画
          let inner = el.getElementsByClassName('inner-hook')[0]; //使用inner-hook类来单纯被js操作
          inner.style.webkitTransform = 'translate3d(0,0,0)';
          inner.style.transform = 'translate3d(0,0,0)';
          el.addEventListener('transitionend', done);//Vue为了知道过渡的完成，必须设置相应的事件监听器。
        });
      },
      afterEnter(el){
        let ball = this.dropBalls.shift(); //完成一次动画就删除一个dropBalls的小球
        if (ball) {
          ball.show = false;
          el.style.display = 'none'; //隐藏小球
        }
      },
      toggleList() {
        if (!this.totalCount) {
          return;
        }
        this.fold = !this.fold;
      },
      empty(){
        this.selectFoods.forEach((food)=>{
          food.count = 0;
        })
      },
      hideList(){
        console.log(!this.fold);
        this.fold = !this.fold;
      },
      pay(){
        if(this.totalPrice < this.minPrice){
          return;
        }
        alert('支付'+this.totalPrice + '元')
      }
    },
    components:{
      cartControl
    }
  }
</script>

<style scoped>
  @import "../../common/css/icon.css";

  .shopcart {
    position: fixed;
    left: 0;
    bottom: 0;
    z-index: 50;
    width: 100%;
    height: 48px;
    background-color: #eee;
  }

  .shopcart .content {
    display: flex;
    background-color: #141d27;
    font-size: 0;
    color:rgba(255, 255, 255, .5);
  }

  .shopcart .content .content-left {
    flex: 1;
  }

  .content .content-left .logo-wrapper {
    display: inline-block;
    position: relative;
    top: -10px;
    margin: 0 12px;
    padding: 6px;
    width: 56px;
    height: 56px;
    box-sizing: border-box;
    vertical-align: top;
    border-radius: 50%;
    background-color: #141d27;
  }
  .logo-wrapper .num {
    position: absolute;
    top: 0;
    right: 0;
    width: 24px;
    height: 16px;
    line-height: 16px;
    text-align: center;
    border-radius: 50%;
    font-size: 9px;
    font-weight: 700;
    color: #fff;
    background-color: rgb(240,20,20);
    box-shadow: 0 4px 8px 0 rgba(0,0,0,.4);
  }
  .content .content-left .logo-wrapper .logo {
    width: 100%;
    height: 100%;
    border-radius: 50%;
    background-color: #2b343c;
    text-align: center;
  }
  .content .content-left .logo-wrapper .logo.highLight{
    background-color: rgb(0,160,220);
  }
  .logo-wrapper .logo.highLight .icon-shopping_cart{
    color: #fff;
  }
  .logo-wrapper .logo .icon-shopping_cart {
    font-size: 24px;
    line-height: 44px;
    color: #80858a;
  }

  .content .content-left .price {
    display: inline-block;
    vertical-align: top;
    margin-top: 12px;
    line-height: 24px;
    box-sizing: border-box;
    padding-right: 12px;
    border-right: 1px solid rgba(255, 255, 255, .1);
    font-size: 16px;
    font-weight: 700;
  }
  .content .content-left .price.highLight{
    color: #fff;
  }
  .content .content-left .desc {
    display: inline-block;
    vertical-align: top;
    line-height: 24px;
    margin: 12px 0 0 12px;
    font-size: 10px
  }

  .shopcart .content .content-right {
    flex: 0 0 105px;
    width: 105px;
  }
  .shopcart .content .content-right .pay{
    height: 48px;
    line-height: 48px;
    text-align: center;
    font-size: 12px;
    background-color: #2b333b;
  }
  .shopcart .content .content-right .pay.not-enough {
    background-color: #2b333b;
  }
  .shopcart .content .content-right .pay.enough {
    background-color: #00b43c;
    color: #fff;
  }
  .ball-container .ball {
    position: fixed;
    left: 32px;
    bottom: 22px;
    z-index: 200;
    transition: all 0.4s cubic-bezier(0.49, -0.29, 0.75, 0.41)
  }
  .ball .inner {
    width: 16px;
    height: 16px;
    border-radius: 50%;
    background: rgb(0, 160, 220);
    transition: all 0.4s linear;
   }
  .shopcart-list {
    position: absolute;
    top:0;
    left: 0;
    transform: translateY(-100%);
    z-index: -1;
    width: 100%;
  }
  .list-ani-enter,.list-ani-leave-to{
    transform: translateY(0);
  }
  .list-ani-enter-active,.list-ani-leave-active{
    transition: all .9s ease;
  }
  .shopcart-list .list-header{
    height: 40px;
    line-height: 40px;
    padding: 0 18px;
    background-color: #f3f5f7;
    border-bottom:1px solid rgba(7,17,27,.1);
  }
  .shopcart-list .list-header .title {
    float: left;
    font-size: 14px;
    color: rgb(7,17,27);
  }
  .shopcart-list .list-header .empty {
    float: right;
    font-size: 12px;
    color: rgb(0, 160, 220);
  }
  .shopcart-list .list-content{
    padding: 0 18px;
    max-height: 217px;
    background-color: #fff;
    overflow: hidden;
  }
  .list-content .food{
    position: relative;
    padding: 12px 0;
    box-sizing: border-box;
  }
  .list-content .food .name {
    line-height: 24px;
    font-size: 14px;
    color: rgb(7,17,27);
  }
  .list-content .food .price{
    position: absolute;
    right: 90px;
    bottom: 12px;
    line-height: 24px;
    font-size: 14px;
    font-weight: 700;
    color: rgb(240,20,20);
  }
  .cartcontrol-warpper {
    position: absolute;
    right: 0;
    bottom: 6px;
  }
  .list-mask {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: -2;
    background-color: rgba(7,17,27,.7);
  }
  .mask-ani-enter,.mask-ani-leave-to{
    opacity: 0;
  }
  .mask-ani-enter-active,.mask-ani-leave-active{
    transition: all .9s ease;
  }
</style>
