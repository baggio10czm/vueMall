<template>
<div class="ratingSelect">
  <div class="rating-type border-bottom1px">
    <span class="block positive" :class="{'active':selectType===2}" @click="select(2)">{{desc.all}}<span class="count">{{ratings.length}}</span></span>
    <span class="block positive" :class="{'active':selectType===0}" @click="select(0)">{{desc.positive}}<span class="count">{{positive.length}}</span> </span>
    <span class="block negative" :class="{'active':selectType===1}" @click="select(1)">{{desc.negative}}<span class="count">{{negative.length}}</span> </span>
  </div>
  <div :class="['switch',{'on':onlyContent}]" @click="switchToggle">
    <i class="icon-check_circle"></i>
    <span class="text">只看有内容的评价</span>
  </div>
</div>
</template>

<script>
  import {ALL, NEGATIVE, POSITIVE} from "config/config"

  export default {
    name: "ratingSelect",
    props: {
      ratings: {
        type: Array,
        default() {
          return [];
        }
      },
      selectType: {
        type: Number,
        default: ALL
      },
      onlyContent: {
        type: Boolean,
        default: false
      },
      desc:{
        type:Object,
        default(){
          return {
            all:'全部',
            positive:"满意",
            negative:"不满意"
          }
        }
      }
    },
    methods:{
      select(type){
        //必须更新父组件的传过来的值 不能直接改父组件传过来的值
        this.$parent.selectType  = type;
      },
      switchToggle(){
        //必须更新父组件的传过来的值 不能直接改父组件传过来的值
        this.$parent.onlyContent = !this.onlyContent
      }
    },
    computed:{
      positive(){
        return this.ratings.filter((rating)=>{
          return rating.rateType === POSITIVE
        })
      },
      negative(){
        return this.ratings.filter((rating)=>{
          return rating.rateType === NEGATIVE
        })
      }
    }
  }
</script>

<style scoped>
  @import "../../common/css/icon.css";
  .ratingSelect{

  }
  .ratingSelect .rating-type {
    padding: 18px 0;
    margin: 0 18px;
    position: relative;
    font-size: 0;
  }
  .ratingSelect .rating-type .block{
    display: inline-block;
    padding: 8px 12px;
    line-height: 16px;
    margin-right: 8px;
    border-radius: 1px;
    color: rgb(77,85,93);
    font-size: 12px;
  }
  .ratingSelect .rating-type .count {
    font-size: 8px;
    margin-left: 2px;
  }
  .rating-type .block.active{
    color: #fff;
  }
  .rating-type .block.positive {
    background-color: rgba(0,160,220,.2);
  }
  .rating-type .block.positive.active {
    background-color: rgb(0,160,220);
  }
  .rating-type .block.negative {
    background-color: rgba(77,85,93,.2);
  }
  .rating-type .block.negative.active {
    background-color: rgb(77,85,93);
  }
  .switch {
    padding:12px 18px;
    line-height: 24px;
    border-bottom: 1px solid rgba(7,17,27,.1);
    color: rgb(147,153,159);
    font-size: 0;
  }
  .switch.on .icon-check_circle{
    color: #00c850;
  }
  .switch .icon-check_circle {
    display: inline-block;
    vertical-align: top;
    font-size: 24px;
    margin-right: 4px;
  }
  .switch .text{
    font-size: 12px;
  }
</style>
