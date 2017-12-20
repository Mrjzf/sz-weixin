<template>
  <div id="collectionCon">

    <header>
      <span class="icon-chs_sywx_ic_jt_left" @click="goBack"></span>
      <span class="title" @dblclick="goTop">我的收藏</span>
      <span class="icon-chs_sywx_ic_delete del" @click="del" v-show="Del"></span>
      <span class="notdel" @click="clearDel" v-show="!Del">取消</span>
    </header>

    <mt-loadmore :bottomMethod="getNext"
                 @bottom-status-change="handleTopChange"
                 :bottom-all-loaded="allLoaded"
                 ref="loadmore">

      <div id="collection_list">

        <div class="everyBlock" v-for="(item, index) in collection_List"
             @click="goContent(item.id, item.showType, item.aaa); choice(index)">

          <span :class="{'icon-chs_sywx_ic_detail_guanb':true,'delMarkFree':true,'delMarkChoice':item.isSel}" v-show="!Del"></span>

          <img :src="'/cms/res/posters/mobile/' + item.posterUrl" alt="">
          <p class="name">{{item.name}}</p>
        </div>

      </div>

      <div slot="bottom" class="mint-loadmore-bottom">
        <div v-show="bottomStatus !== 'loading'">
          <span :class="{ 'rotate': bottomStatus === 'drop' }">↑</span>
          <span v-if="bottomStatus === 'pull'">&nbsp;上拉加载</span>
          <span v-else>&nbsp;释放加载</span>
        </div>
        <span v-show="bottomStatus === 'loading'">Loading...</span>
      </div>
      <div id="dataEnd" v-show="allLoaded">没有更多了！</div>

    </mt-loadmore>

    <div id="allSel" v-show="!Del">
      <p>
        <span @click="allChoice">全选</span>
      </p>
      <div></div>
      <p>
        <span @click="delData">删除</span>
      </p>
    </div>

    <div id="footerHeight" v-show="!Del"></div>

    <div id="nothing" v-show="isNothingShow">
      <img src="../../../assets/images/nothing.png" alt="">
      <p>空空如也~</p>
    </div>

  </div>
</template>

<script>
  import { Toast } from 'mint-ui';

  export default {
    data(){
      return {
        isNothingShow: false,
        Del: true,
        collection_List: [],
        allLoaded: false,
        bottomStatus: "",
        pageNum: 1,
        totalPage: 0,
        toast: null,
        delMarkCss: "delMarkFree",
        delArr: "",    //删除列表
        isAllSel: false //是不是全选了
      }
    },
    methods: {
      handleTopChange(Status) {
        this.bottomStatus = Status;
      },
      goBack() {
        this.$router.go(-1);
      },
      getCollectionList() {

        this.toast = Toast({
          message: '加载中...',
          position: 'middle',
          duration: -1
        });

        var url = '/cms/thirdPartyPortalInterfaceV2/listCollection.service?clientId=' + this.$store.state.openid + '&terminalType=mobile&videoType=vpg&pageSize=15';
        this.$http.get(url).then(res => {
          var data = res.body;

          data.videoList.forEach((v, i) => {
            v.isSel = false;
            this.collection_List.push(v);
          })

          if (data.pageInfo.totalNum == 0) {
            this.isNothingShow = true;  //显示 “空空如也” !
          }
          this.totalPage = data.pageInfo.totalPage;
          this.collection_List = data.videoList;
          this.toast.close();
        }, res => {

        })
      },
      getNext() {
        this.pageNum += 1;
        this.isAllSel = false;

        if (this.totalPage == 0) {
          return
        }
        if (this.pageNum > this.totalPage) {
          this.allLoaded = true;
          return
        }

        var url = '/cms/thirdPartyPortalInterfaceV2/listCollection.service?clientId=' + this.$store.state.openid + '&terminalType=mobile&videoType=vpg&pageSize=15&pageNum=' + this.pageNum;

        this.$http.get(url).then(res => {
          var data = res.body;
          data.videoList.forEach((v, i) => {
            v.isSel = false;
            this.collection_List.push(v);
          })
          this.$refs.loadmore.onBottomLoaded();
        }, res => {

        })
      },
      del() {
        this.Del = false;
      },
      clearDel() {
        this.Del = true;
        this.collection_List.forEach((v, i) => {
          v.isSel = false;
        })
        this.delArr = "";
        this.isAllSel = false;
      },
      choice(_index) {
        if (this.Del == true) {
          return
        }
        var _v = this.collection_List[_index];
        _v.isSel = _v.isSel ? false : true;
        if (_v.isSel == true) {
          this.delArr += _v.id + ",";
        } else {
          this.delArr = this.delArr.replace(_v.id + ",", "");
        }
      },
      allChoice() {
        if (this.isAllSel == false) {
          this.delArr = "";
          this.collection_List.forEach((v, i) => {
            v.isSel = true;
            this.delArr += v.id + ",";
          })
          this.isAllSel = true;
        } else {
          this.collection_List.forEach((v, i) => {
            v.isSel = false;
            this.delArr = "";
          })
          this.isAllSel = false;
        }
      },
      delData() {

        this.toast = Toast({
          message: '删除中...',
          position: 'middle',
          duration: -1
        });

        var url = '/cms/thirdPartyPortalInterfaceV2/removeCollection.service?clientId=' + this.$store.state.openid + '&videoType=vpg&id=' + this.delArr;

        this.$http.get(url).then(res => {
          var data = res.body;
          console.log(data);
          this.getCollectionList();
          this.collection_List = this.collection_List;

          this.toast.close();
        }, res => {
          alert("删除失败!");
        })
      },
      goContent(id, type, isZhgq){
        if (this.Del != true) {
          return
        }
        this.$router.push({path: 'detail', query: {id: id, type: type, isZhgq: isZhgq}});
      },
      goTop() {
        if (document.body.scrollTop > 0) {
          var timer = setInterval(function () {
            document.body.scrollTop -= 35;
            if (document.body.scrollTop <= 0) {
              document.body.scrollTop = 0;
              clearInterval(timer);
            }
          }, 15)
        } else {
          return
        }
      }
    },
    filters: {
      isActive(v1, v2, v3){
        return v2 == v3 ? v1 + " delMarkChoice" : v1 + " delMarkFree";
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

  p {
    margin: 0;
    padding: 0;
  }

  header {
    width: 100%;
    height: 8vh;
    text-align: center;
    line-height: 8vh;
    font-size: 3vh;
    border-bottom: 2px solid #FF3E05;
    background-color: #fff;
    position: fixed;
    z-index: 10;
  }
  header .icon-chs_sywx_ic_jt_left {
    font-size: 3.5vh;
    color: #FF3E05;
    position: absolute;
    left: 3vh;
    top: 2.25vh;
  }
  header .del {
    font-size: 4vh;
    color: #FF3E05;
    position: absolute;
    right: 3vh;
    top: 2vh;
  }
  header .notdel {
    font-size: 2.5vh;
    color: #FF3E05;
    position: absolute;
    right: 3vh;
  }

  #collection_list {
    padding: 2vw;
    overflow: hidden;
  }
  #collection_list .delMarkFree {
    font-size: 5vh;
    color: #797979;
  }
  #collection_list .delMarkChoice {
    font-size: 5vh;
    color: #FF3E05;
  }

  .everyBlock {
    width: 32%;
    float: left;
    margin-right: 2%;
  }
  .everyBlock:nth-child(3n) {
    margin-right: 0;
  }
  .everyBlock img {
    border-radius: 2vw;
    height: 46vw;
  }
  .name {
    width: 100%;
    clear: both;
    white-space: nowrap;
    text-overflow: ellipsis;
    overflow: hidden;
    font-size: 14px;
    margin: 4px 0 4px 0;
    text-align: center;
    color: #212121;
  }

  #allSel {
    width: 100%;
    height: 8vh;
    position: fixed;
    bottom: 0;
    z-index: 10;
    background-color: #FFFFFF;
    border-top: 1px solid #e4e4e4;
  }
  #allSel p {
    width: 49.5%;
    height: 100%;
    text-align: center;
    font-size: 3vh;
  }
  #allSel p:nth-child(1) {
    width: 49.5%;
    height: 100%;
    line-height: 8vh;
    text-align: center;
    float: left;
  }
  #allSel p:nth-child(1) span {
    display: block;
    width: 20vw;
    height: 5.6vh;
    line-height: 6vh;
    transform: translateX(14.5vw) translateY(1vh);
    /*border: 1px solid #00C72E;*/
  }
  #allSel div {
    width: 0.5%;
    height: 7vh;
    transform: translateY(0.5vh);
    background-color: #b3b3b3;
    float: left;
    opacity: 0.3;
  }
  #allSel p:nth-child(3) {
    width: 49.5%;
    height: 100%;
    line-height: 8vh;
    text-align: center;
    float: right;
  }
  #allSel p:nth-child(3) span {
    display: block;
    width: 20vw;
    height: 5.6vh;
    line-height: 6vh;
    transform: translateX(14.5vw) translateY(1vh);
    /*border: 1px solid #00C72E;*/
    color: #FF3E05;
  }
  #footerHeight {
    margin-bottom: 8vh;
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

  .mint-loadmore {
    margin-top: 8vh;
  }
  .mint-loadmore-bottom span {
    display: inline-block;
    vertical-align: middle;
    transition: all .25s;
  }
  .rotate {
    transform: rotate(180deg);
  }

  #dataEnd {
    width: 100%;
    height: 50px;
    background-color: #e3e3e3;
    text-align: center;
    line-height: 50px;
  }
</style>
