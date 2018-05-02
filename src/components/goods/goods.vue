<template>
  <div class="goods">
    <div class="menu-wrapper" ref="menuWrapper">
      <ul>
        <li v-for="(item,index) in goods" class="menu-item" :class="{'current':currentIndex === index}" @click="selectMenu(index)">
          <span class="text border-bottom1px">
            <span v-if="item.type > 0" :class="['icon',classNameArr[item.type]]"></span>{{item.name}}
          </span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" ref="foodsWrapper">
      <ul>
        <li v-for="item in goods" class="food-list food-list-hook">
          <h2 class="title">{{item.name}}</h2>
          <ul>
            <li v-for="food in item.foods" class="food-item border-bottom1px" @click="selectFoodFn(food)">
              <div class="icon">
                <img width="57" height="57" :src="food.icon" alt="">
              </div>
              <div class="content">
                <h3 class="name">{{ food.name}}</h3>
                <p class="desc">{{food.description}}</p>
                <div class="extra">
                  <span class="count">月售{{food.sellCount}}份</span>
                  <span>好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span class="now">¥{{food.price}}</span>
                  <span class="old" v-if="food.oldPrice">¥{{food.oldPrice}}</span>
                </div>
                <div class="cartControl-wrapper">
                  <cartControl :food="food" @addCart="getControlDom"></cartControl>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <shopCart ref="shopCart" :selectFoods="selectFoods" :deliveryPrice="seller.deliveryPrice" :minPrice="seller.minPrice"></shopCart>
    <food :food="selectedFood" ref="foodComponent" @addCart="getControlDom"></food>
  </div>
</template>

<script>
  import {ERR_OK} from 'config/config.js'
  import axios from 'axios'
  import BScroll from 'better-scroll'
  import shopCart from 'components/shopcart/shopcart'
  import cartControl from 'components/cartcontrol/cartcontrol'
  import food from 'components/food/food'

  export default {
    name: "goods",
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        classNameArr: ['decrease', 'discount', 'special', 'invoice', 'guarantee'],
        goods: [],
        meunScroll: null,
        foodsScroll: null,
        listHeight: [],
        scrollY: 0,
        food:{},
        selectedFood:{}
      }
    },
    created() {
      axios.get('api/goods').then((res) => {
        if (res.data.errno === ERR_OK) {
          this.goods = res.data.data;
          this.$nextTick(()=>{
            this._initScroll();
            this._calculateHeight();
          });
        }
      })
    },
    computed: {
      currentIndex() {
        for (let i = 0; i < this.listHeight.length; i++) {
          let height1 = this.listHeight[i];
          let height2 = this.listHeight[i + 1];
          if (!height2 || this.scrollY >= height1 && this.scrollY < height2) {
            return i;
          }
        }
        return 0;
      },
      selectFoods(){
        let foods = [];
        this.goods.forEach((good)=>{
          good.foods.forEach((food)=>{
            if(food.count){
              foods.push(food)
            }
          })
        });
        return foods;
      }
    },
    methods: {
      _initScroll() {
        this.meunScroll = new BScroll(this.$refs.menuWrapper, {
          click:true
        });
        this.foodsScroll = new BScroll(this.$refs.foodsWrapper, {
          click:true,
          probeType: 3
        });
        this.foodsScroll.on('scroll', (pos) => {
          this.scrollY = Math.abs(Math.round(pos.y))
        })
      },
      _calculateHeight() {
        let foodsList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook');
        let height = 0;
        this.listHeight.push(height);
        for (let i = 0; i < foodsList.length; i++) {
          height += foodsList[i].clientHeight;
          this.listHeight.push(height);
        }
      },
      selectMenu(index){
        this.foodsScroll.scrollTo(0,-this.listHeight[index],500);
      },
      getControlDom(el){
        this.$refs.shopCart.drop(el);
      },
      selectFoodFn(food){
        this.selectedFood = food;
        this.$refs.foodComponent.show();
      }
    },
    components:{
      shopCart,
      cartControl,
      food
    }
  }
</script>

<style scoped>
  .goods {
    display: flex;
    position: absolute;
    top: 174px;
    bottom: 46px;
    width: 100%;
    overflow: hidden;
  }

  .goods .menu-wrapper {
    flex: 0 0 80px;
    width: 80px;
    background-color: #f3f5f7;
  }

  .goods .menu-wrapper .menu-item {
    display: table;
    height: 54px;
    width: 60px;
    line-height: 14px;
    padding: 0 10px;
  }

  .goods .menu-wrapper .menu-item.current {
    position: relative;
    margin-top: -1px;
    z-index: 10;
    background: #fff;
    font-weight: 700;
  }

  .goods .menu-wrapper .menu-item.current .text {
    font-weight: bold;
  }

  .goods .menu-wrapper .menu-item.current .text:after {
    border-bottom: none !important;
  }

  .goods .menu-wrapper .menu-item .icon {

  }

  .goods .menu-wrapper .menu-item .icon {
    display: inline-block;
    width: 12px;
    height: 12px;
    vertical-align: top;
    margin-right: 2px;
    background-size: 12px 12px;
    background-repeat: no-repeat;
  }

  .goods .menu-wrapper .menu-item .icon.decrease {
    background-image: url('decrease_3@2x.png');
  }

  .goods .menu-wrapper .menu-item .icon.discount {
    background-image: url('discount_3@2x.png');
  }

  .goods .menu-wrapper .menu-item .icon.guarantee {
    background-image: url('guarantee_3@2x.png');
  }

  .goods .menu-wrapper .menu-item .icon.invoice {
    background-image: url('invoice_3@2x.png');
  }

  .goods .menu-wrapper .menu-item .icon.special {
    background-image: url('special_3@2x.png');
  }

  .goods .menu-wrapper .menu-item .text {
    display: table-cell;
    width: 60px;
    vertical-align: middle;
    font-size: 12px;
    position: relative;
  }

  .goods .foods-wrapper {
    flex: 1;
  }

  .goods .foods-wrapper .title {
    padding-left: 14px;
    height: 26px;
    line-height: 26px;
    border-left: 1px solid #d9dde1;
    font-size: 12px;
    color: rgb(147, 153, 159);
    background-color: #f3f5f7;
  }

  .goods .foods-wrapper .food-item {
    position: relative;
    display: flex;
    margin: 18px;
    padding-bottom: 15px;
  }

  .goods .foods-wrapper .food-item:last-child {
    border-bottom: none !important;
    margin-bottom: 0;
  }

  .foods-wrapper .food-item .icon {
    flex: 0 0 57px;
    width: 57px;
    margin-right: 10px;
  }

  .foods-wrapper .food-item .content {
    flex: 1;
  }

  .foods-wrapper .food-item .content .name {
    margin: 2px 0 8px;
    height: 14px;
    line-height: 14px;
    font-size: 14px;
    color: rgb(7, 17, 27);
  }

  .foods-wrapper .food-item .content .desc, .extra {
    line-height: 10px;
    font-size: 10px;
    color: rgb(147, 153, 159);
  }

  .foods-wrapper .food-item .content .desc {
    margin-bottom: 8px;
    line-height: 14px;
  }

  .food-item .content .extra .count {
    margin-right: 12px;
  }

  .food-item .content .price {
    font-weight: 700;
    line-height: 24px;
  }

  .food-item .content .price .now {
    margin-right: 8px;
    font-size: 14px;
    color: rgb(240, 20, 20);
  }

  .food-item .content .price .old {
    margin-right: 8px;
    font-size: 10px;
    color: rgb(147, 153, 159);
  }
  .cartControl-wrapper {
    position: absolute;
    right: 0;
    bottom: 12px;
  }
</style>
