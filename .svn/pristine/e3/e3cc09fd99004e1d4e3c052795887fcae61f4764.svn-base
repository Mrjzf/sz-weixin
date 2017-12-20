<template>
  <div id="Rec2Con">

    <header>
      <span class="icon-chs_sywx_ic_jt_left" @click="goback();"></span>
      {{ title }}
      <span class="icon-chs_sywx_ic_search" @click="gosearch();"></span>
    </header>

    <div id="Rec2two">
      <div class="everyBlock" v-for="(item, index) in conList" @click="goContent(item.contentId, item.showType);">
        <span class="phoneMark" v-show="item.mobileScreen==1">手机看</span>
        <img :src="item.poster | imgChange" alt="">
        <p class="name">{{item.title}}</p>
      </div>
    </div>

    <!--<div id="footerHeight"></div>-->

  </div>
</template>

<script>

  export default {
    data(){
      return {
        id: this.$route.query.id,
        conList: [],
        title: "",
        local_vpg_video: [],
        vpg_video: []
      }
    },
    created(){
        this.getCon();
    },
    methods: {
      getCon(){

        var url = "/cms/thirdPartyPortalInterfaceV2/getTopicById.service?topicId=" + this.id;

        this.$http.get(url).then(res => {
          var data = res.body;

          this.title = data.title;
          this.conList = data.contentList;

        }, res => {
          console.log("错误!" + res);
        })
      },
      goContent(_id, showType){

        this.$router.push({path: "/detail", query: {id: _id, showType: showType}});
      },
      goback(){
        this.$router.go(-1);
      },
      gosearch(){
        this.$router.push({path: '/search'});
      }
    },
    components: {
    },
    filters: {
      imgChange(v1){
        var s = Math.random();
        return "/cms/res/posters/mobile/" + v1 + "?s=" + s;
      }
    }
  }
</script>

<style scoped>

  #Rec2two {
    overflow: hidden;
    margin-top: 8.5vh;
    padding: 0px 8px 8px 8px;
    background-color: #fff;
  }

  header {
    position: fixed;
    top: 0;
    z-index: 999;
    width: 100vw;
    height: 7vh;
    background-color: #FF3E05;
    text-align: center;
    color: white;
    line-height: 7vh;
    font-size: 2.5vh;
  }
  header .icon-chs_sywx_ic_jt_left {
    display: block;
    height: 100%;
    float: left;
    font-size: 3vh;
    line-height: 7vh;
    margin-left: 3%;
  }
  header .icon-chs_sywx_ic_search {
    display: block;
    height: 100%;
    float: right;
    font-size: 4vh;
    line-height: 7vh;
    margin-right: 3%;
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

  #footerHeight {
    margin-bottom: 8vh;
  }

</style>
