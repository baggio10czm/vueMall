<template>
  <div class="star" :class="starType">
    <span v-for="itemClass in itemClsses" :class="['star-item',itemClass]"></span>
  </div>
</template>

<script>
  const LENGTH = 5;
  const GLS_ON = 'on';
  const GLS_OFF = 'off';
  const GLS_HALF = 'half';
  export default {
    name: "star",
    props: {
      size: {
        type: Number
      },
      score: {
        type: Number
      }
    },
    data() {
      return {}
    },
    computed: {
      starType() {  // 根据传进来的 size 生成对应的 CLASS
        return 'star-' + this.size
      },
      itemClsses(){  // 根据分数生成对应的 class 数组
        let res = [];
        let score = Math.floor(this.score * 2) / 2;
        let hasDecimal = score % 1 !== 0;    // 判断是否有 半星
        let integer = Math.floor(score);
        for (let i = 0; i < integer; i++) {
          res.push(GLS_ON);
        }
        if(hasDecimal){
          res.push(GLS_HALF);
        }
        while (res.length < LENGTH){   // 当数量不够 拿空星补够
          res.push(GLS_OFF);
        }
        return res;
      }
    }
  }
</script>

<style scoped>
  .star {
    font-size: 0;
  }
  .star .star-item{
    display: inline-block;
    background-repeat: no-repeat;
  }
  .star span:last-child{
    margin-right: 0;
  }
  .star.star-48 .star-item{
    width: 20px;
    height: 20px;
    margin-right: 22px;
    background-size:20px 20px ;
  }
  .star.star-48 .star-item.on {
    background-image: url("star48_on@2x.png");
  }
  .star.star-48 .star-item.half {
    background-image: url("star48_half@2x.png");
  }
  .star.star-48 .star-item.off {
    background-image: url("star48_off@2x.png");
  }
  .star.star-36 .star-item{
    width: 15px;
    height: 15px;
    margin-right: 6px;
    background-size:15px 15px ;
  }
  .star.star-36 .star-item.on {
    background-image: url("star36_on@2x.png");
  }
  .star.star-36 .star-item.half {
    background-image: url("star36_half@2x.png");
  }
  .star.star-36 .star-item.off {
    background-image: url("star36_off@2x.png");
  }
  .star.star-24 .star-item{
    width: 10px;
    height: 10px;
    margin-right: 3px;
    background-size:10px 10px ;
  }
  .star.star-24 .star-item.on {
    background-image: url("star36_on@2x.png");
  }
  .star.star-24 .star-item.half {
    background-image: url("star36_half@2x.png");
  }
  .star.star-24 .star-item.off {
    background-image: url("star36_off@2x.png");
  }
</style>
