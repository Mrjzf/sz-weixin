<template>
  <div id="category_con">

    <div class="everyBlock" v-for="(item, index) in data" @click="goContent(item.Id, item.showType);">
      <span class="phoneMark" v-show="item.mobileScreen==1">手机看</span>
      <img :src="'/cms/res/posters/mobile/' + item.poster" alt="">
      <p class="name">{{item.name}}</p>
    </div>

    <div id="nothing" v-show="data==undefined">
      <img src="../../../assets/images/nothing.png" alt="">
      <p>搜索暂无结果~</p>
    </div>

  </div>

</template>

<script>

  export default {
    data(){
      return {

      }
    },
    props: ["data"],
    methods: {
      goContent(id, showType){
        this.$router.push({path: '/detail', query: {id: id, showType: showType}});
      }
    }
  }
</script>

<style scoped>
  #category_con {
    overflow: hidden;
    /*padding: 0px 8px 8px 8px;*/
    /*margin-top: 8px;*/
  }

  /*每小块*/
  .everyBlock {
    width: 32%;
    position: relative;
    float: left;
    margin-right: 2%;
  }
  .everyBlock .phoneMark {
    position: absolute;
    right: 0;
    width: 40%;
    height: 5vw;
    line-height: 5vw;
    text-align: center;
    font-size: 12px;
    background-color: #91cc2b;
    color: #fff;
    border-radius: 0 0 0 10px;
  }
  .everyBlock img {
    height: 46vw;
  }
  .everyBlock:nth-child(3n) {
    margin-right: 0;
  }
  .name {
    width: 100%;
    clear: both;
    white-space:nowrap;
    text-overflow:ellipsis;
    overflow:hidden;
    font-size: 14px;
    margin: 4px 0 4px 0;
    text-align: center;
    color: #212121;
  }

  #nothing {
    margin-top: 15vh;
    background-color: #fff;
  }
  #nothing img {
    width: 40vw;
    margin: 0 auto;
  }
  #nothing p {
    margin-top: 10px;
    text-align: center;
    font-size: 16px;
  }
</style>
