<template>
  <div id="app">
    <v-header :seller="seller"></v-header>
    <div class="tab">
      <div class="tab-item">
        <router-link to="/goods">商品</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/ratings">评论</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/seller">商家</router-link>
      </div>
    </div>
    <router-view :seller="seller"></router-view>

  </div>
</template>

<script>
 import header from './components/header/header.vue';

 const ERR_OK = 0;// if server ruturns 0 ,it means this ajax is right;

 export default {
  data() {
    return {
      seller: {}
    };
  },
  created() {
    this.$http.get('/api/seller').then(function(res) {
      res = res.body;// 得到它的json文件
      if (res.errno === ERR_OK) {
        this.seller = res.data;
        // console.log(this.seller);
      }
    });
  },
  components: {
    'v-header': header
 }
};
</script>

<style type="text/css">

#app .tab{
    display: flex;
    width: 100%;
    height: 40px;
    line-height: 40px;
    border-bottom: 1px solid rgba(7,17,27,0.1);
    /*border-1px(rgba(7,17,27,0.1));*/
  }

  .tab-item{
    flex: 1;
    font-size: 14px;
    text-align: center;
    color:rgb(77,85,93);
  }
  
  .tab-item>a{
    display: block
  }
  
  .router-link-active{
    color: rgb(240,20,20)
  }
  
</style>