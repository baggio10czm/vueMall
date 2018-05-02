<template>
  <div id="app">
    <mallHeader :seller="seller"></mallHeader>
    <div class="tab border-bottom1px">
      <div class="tab-item"><router-link to="/goods">商品</router-link></div>
      <div class="tab-item"><router-link to="/ratings">评论</router-link></div>
      <div class="tab-item"><router-link to="/seller">商家</router-link></div>
    </div>
    <keep-alive>
      <router-view :seller="seller"></router-view>
    </keep-alive>
  </div>
</template>

<script>
  import mallHeader from "components/mallHeader/mallHeader.vue"
  import {ERR_OK} from 'config/config'
  import axios from 'axios'
  import {urlParse} from 'common/js/util'
export default {
  name: 'App',
  data(){
    return{
      seller:{
        id:(()=>{
          let queryParam = urlParse();
          return queryParam.id
        })()
      }
    }
  },
  created(){
    axios.get('api/seller?id='+ this.seller.id).then((res)=>{
        if(res.data.errno === ERR_OK){
          this.seller = Object.assign({}, this.seller, res.data.data);
        }
    })
  },
  components:{
    mallHeader
  },

}
</script>

<style>
  @import "common/css/base.css";
  .tab {
    display: flex;
    width: 100%;
    height: 40px;
    line-height: 40px;
    position: relative;
  }
  .tab .tab-item{
    flex: 1;
    text-align: center;
  }
.tab .tab-item a {
  display: block;
  font-size: 14px;
  color: rgb(77,85,93);
}
.tab .tab-item a.tabs-active {
  color: rgb(240,20,20);
 }
</style>
