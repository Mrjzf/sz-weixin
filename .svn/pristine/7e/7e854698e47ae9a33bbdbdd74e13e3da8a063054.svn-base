<template>
  <div id="VOD_con">

    <div class="everyBlock" v-for="(item, index) in data" @click="goContent(item.id, item.showType)">
      <span class="phoneMark" v-show="item.mobileScreen==1">手机看</span>
      <img :src="'/cms/res/posters/mobile/' + item.posterUrl" alt="">
      <p class="name">{{item.name}}</p>
    </div>

  </div>
</template>

<script>

  export default {
    data(){
      return {
        VOD_conList:[

        ]
      }
    },
    props:['data'],
    methods: {
      goContent(id, showType){
        this.$router.push({path: 'detail', query: {id: id, showType: showType}});
      }
    }
  }
</script>

<style scoped>

  #VOD_con {
    overflow: hidden;
    background-color: #fff;
    padding: 8px 8px 0 8px;
  }
  img {
    width: 100%;
    border-radius: 5px;
  }

  /*每小块*/
  .everyBlock {
    width: 32%;
    float: left;
    margin-right: 2%;
    position: relative;
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
</style>
