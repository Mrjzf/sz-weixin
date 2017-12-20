<template>
  <div id="collectionCon">

    <header>
      <span class="icon-chs_sywx_ic_jt_left" @click="goBack"></span>
      <span class="title">我的收藏</span>
      <span class="del" @click=""></span>
    </header>

    <div id="nothing" v-show="isNothingShow">
      <img src="../../../assets/images/nothing.png" alt="">
      <p>空空如也~</p>
    </div>

  </div>
</template>

<script>
  export default {
    data(){
      return {
        isNothingShow: false,
        collection_List: []
      }
    },
    methods: {
      goBack() {
        this.$router.go(-1);
      },
      getCollectionList() {
        var url = '/cms/thirdPartyPortalInterfaceV2/listCollection.service?clientId=' + this.$store.state.openid + '&terminalType=mobile&videoType=vpg';
        this.$http.get(url).then(res => {
          var data = res.body;
          console.log(data);
          this.collection_List = data.videoList;
          if (this.collection_List.length == 0) {
            this.isNothingShow = true; //显示 “空空如也” !
          }
        }, res => {

        })
      }
    },
    created() {
      this.getCollectionList();
    }
  }
</script>

<style scoped>
  #collectionCon {
    background-color: #fff;
    overflow: hidden;
  }

  header {
    width: 100%;
    height: 8vh;
    text-align: center;
    line-height: 8vh;
    font-size: 3vh;
    border-bottom: 2px solid #FF3E05;
    background-color: #fff;
    position: relative;
  }
  header .icon-chs_sywx_ic_jt_left {
    font-size: 3.5vh;
    color: #FF3E05;
    position: absolute;
    left: 3vh;
    top: 2.25vh;
  }
  header .del {
    font-size: 2.5vh;
    color: #FF3E05;
    position: absolute;
    right: 3vh;
    /*top: 2.25vh;*/
  }

  #nothing {
    margin-top: 20vh;
    background-color: #fff;
  }
  #nothing img {
    width: 40vw;
    margin: 0 auto;
  }
  #nothing p {
    margin-top: 10px;
    text-align: center;
    font-size: 3.5vh;
    color: #cacaca;
  }
</style>
