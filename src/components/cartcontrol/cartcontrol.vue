<template>
  <div class="cartControl">
    <transition name="decrease-ani">
      <div class="cart-decrease icon-remove_circle_outline" v-show="food.count > 0" @click.stop="decreaseCart"></div>
    </transition>
    <transition name="count-ani">
    <div class="cart-count" v-show="food.count > 0">{{food.count}}</div>
    </transition>
    <div class="cart-add icon-add_circle" @click.stop="addCart($event)" style="position: relative;"></div>
  </div>
</template>

<script>
  export default {
    name: "cartControl",
    props: {
      food: {
        type: Object
      }
    },
    methods: {
      addCart(event) {
        if (!this.food.count) {  // 当没有count的时候，用$set触发dom变化
          this.$set(this.food, 'count', 1);
        } else {
          this.food.count++;
        }
        this.$emit('addCart',event.target);
      },
      decreaseCart() {
        if (this.food.count) {
          this.food.count--;
        }
      }
    }
  }
</script>

<style scoped>
  @import "../../common/css/icon.css";

  .cartControl {
    font-size: 0;
  }

  .cart-decrease, .cart-add, .cart-count {
    display: inline-block;
    font-size: 24px;
    line-height: 24px;
    color: rgb(0, 160, 220);
  }

  .cart-count {
    display: inline-block;
    vertical-align: top;
    width: 12px;
    padding-top: 6px;
    line-height: 24px;
    text-align: center;
    font-size: 10px;
    color: rgb(147, 153, 159);
  }

  .cart-decrease, .cart-add {
    padding: 6px;
  }
  .decrease-ani-enter,.decrease-ani-leave-to{
    opacity: 0;
    transform: translateX(40px) rotate(180deg);
  }
  .decrease-ani-enter-active,.decrease-ani-leave-active{
    transition: all .9s ease;
  }
  .count-ani-enter,.count-ani-leave-to{
    opacity: 0;
  }
  .count-ani-enter-active,.count-ani-leave-active{
    transition: all 1.5s ease;
  }
</style>
