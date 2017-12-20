<template>
  <div id="channel_show">

    <aside>
      <!-- 这里将index作为参数传到changeActive里去 -->
      <div class="swiper-container">
        <div class="swiper-wrapper">
          <div v-for="(item, index) in day" :class="{active:isActive==index}"
               @click="getShowMsg(index, item.dateInit)" class="every_swiper swiper-slide">{{item.date}}
            <div class="dash" v-show="isActive==index"></div>
          </div>
        </div>
      </div>
    </aside>

    <div id="content">

      <div class="swiper-container">
        <div class="swiper-wrapper">
          <div v-for="(item, index) in showlist" :class="'swiper-slide' | isClick(isClick, index)"
               @click="huikan(item.name, item.assetId, item.status, index, item.startTime, item.livePoster);">
            <h1 :class="color | color(item.status, clickOnce, index, isClick)">
              {{item.startTime | timeInit}}&nbsp;&nbsp;&nbsp;{{item.name}}
            </h1>
            <button :class="'state' | color2(item.status, clickOnce, isClick, index)">{{item.status | statusJudge}}
            </button>
          </div>
        </div>
      </div>

    </div>

  </div>
</template>

<script>
  import {Toast} from 'mint-ui';

  export default {
    data: function () {
      return {
        day: [],
        showlist: [],
        tolivehead: 2,
        isActive: 0,
        startX: 0,
        endX: 0,
        changeX: 0,
        startY: 0,
        endY: 0,
        changeY: 0,
        color: "",
        shengchannelId: this.$route.query.shengchannelId,
        channelId: this.$route.query.channelId,
        isClick: "",
        clickOnce: false,
        status: "1",
        whichTime: undefined,
        toast: null,
        isInit:true,
        statusHave1: false,    //status是否存在1
        huikanFirstinit: true,
        assetID: ""
      }
    },
    components: {
    },
    props: ["channelName"],
    created(){
      this.dateInit();
      this.getShowMsg();
    },
    mounted(){
      this.isInit = true;
      this.Swiper();
      this.Swiper2();
    },
    updated(){

    },
    destroyed(){
      this.$store.commit("videoAddress", undefined);
      this.$store.commit("videoAddress2", undefined);
    },
    methods: {
      changeActive: function (index, date) {
        this.isActive = index;
        this.isClick = "asd";

        var channelId = this.$route.query.channelId;
        var url = "/cms/thirdPartyPortalInterfaceV2/getProgramList.service?clientId=" + this.$store.state.userID + "&date=" + date + "&channelId=" + channelId + "&terminalType=mobile&resFormat=json"
        this.$http.get(url).then(res => {
          var data = res.body;
          this.showlist = data.programList;
          for (var i = 0; i < this.showlist.length; i++) {
            if (this.showlist[i].status == 1) {
              this.whichTime = this.showlist[i].startTime.substring(9);
            }
          }
        }, res => {
          console.log("错误!");
        })
      },
      getShowMsg(index, date){
        if (this.$route.query.searchType == "huikan" && this.huikanFirstinit == true) {
          //使搜索回看点进来第一次的机制不成立
          this.huikanFirstinit = false;
          //获取回看节目的日期和索引。并实现UI效果
          for (var i = 0; i < this.day.length; i++) {
            if (this.$route.query.alldate == this.day[i].dateInit) {
              this.isActive = i;
            }
          }
          var date = this.$route.query.alldate;   //获取整的日期（如：20170601）
          var name = this.$route.query.huiName;   //搜索回看的节目名称
//          var huiTime = this.$route.query.huiTime;   //搜索回看的节目名称
//          var huiTime2 = "";   //搜索回看的节目名称
//
          var assetid = this.$route.query.assetId;
//
//          if (huiTime.length <= 14) {
//            for (let i = 0; i < huiTime.length; i++) {
//              huiTime2 += huiTime[i];
//              if (i == 7) {
//                huiTime2 += " ";
//              }
//              if (i == 9) {
//                huiTime2 += ":";
//              }
//              if (i == 11) {
//                huiTime2 += ":";
//              }
//            }
//          } else {
//            huiTime2 = huiTime;
//          }
          var liveAddressurl = "/cms/thirdPartyPortalInterface/play.service?resFormat=json&terminalType=mobile&channelId=" + this.$route.query.channelId + "&clientId=" + this.$store.state.userID + "&busiType=live&definition=sd&clientip=1.1.1.1";
          this.$http.get(liveAddressurl).then(res => {
            var data = res.body;
            var u = data.playResponse.contentList.content[0].url;
//              this.$store.commit("videoAddress", u);
            this.$store.commit("videoAddress2", u); //+ "?delay=30"

            //加载这天的节目单
            var channelId = this.$route.query.channelId;  //获取频道id
            var url = "/cms/thirdPartyPortalInterfaceV2/getProgramList.service?clientId=" + this.$store.state.userID + "&date=" + date + "&channelId=" + channelId + "&terminalType=mobile&resFormat=json";
            this.$http.get(url).then(res => {
              var data = res.body;
              //赋值节目单
              this.showlist = data.programList;

              for (var i = 0; i < this.showlist.length; i++) {;
                if (this.showlist[i].assetId == assetid) {
                  this.clickOnce = true;
//                this.isClick = i;
                  var item = this.showlist[i];

                  this.huikan(item.name, item.assetId, item.status, i, item.startTime, item.livePoster);
                }
              }
            }, res => {
              Toast("节目单加载错误!");
            })
          }, res => {

          })

        }
        else {
          index = index == undefined ? 0 : index;
          this.isActive = index;
          this.isClick = "asd";

          this.toast = Toast({
            message: '请耐心等待视频加载...',
            position: 'middle',
            duration: -1
          });
          var time = new Date().getFullYear() + "" + (new Date().getMonth() + 1 < 10 ? "0" + (new Date().getMonth() + 1) : (new Date().getMonth() + 1)) + "" + (new Date().getDate() + 1 < 10 ? "0" + new Date().getDate() : new Date().getDate());

          var channelId = this.$route.query.channelId;

          if (channelId == undefined) {
            channelId = 204455;
          }
          var url;
          if (date == undefined) {
            url = "/cms/thirdPartyPortalInterfaceV2/getProgramList.service?clientId=" + this.$store.state.userID + "&date=" + time + "&channelId=" + channelId + "&terminalType=mobile&resFormat=json";
          } else {
            url = "/cms/thirdPartyPortalInterfaceV2/getProgramList.service?clientId=" + this.$store.state.userID + "&date=" + date + "&channelId=" + channelId + "&terminalType=mobile&resFormat=json";
          }

          //说明是从APP那分享过来的
          if (this.$route.query.from == "mobile" || this.$route.query.liveBroadcastAddress == undefined) {

            var liveAddressurl = "/cms/thirdPartyPortalInterface/play.service?resFormat=json&terminalType=mobile&channelId=" + this.$route.query.channelId + "&clientId=" + this.$store.state.userID + "&busiType=live&definition=sd&clientip=1.1.1.1";
            this.$http.get(liveAddressurl).then(res => {
              var data = res.body;
              var u = data.playResponse.contentList.content[0].url;
//              this.$store.commit("videoAddress", u);
              this.$store.commit("videoAddress2", u); //+ "?delay=30"

              //获取节目清单
              this.$http.get(url).then(res => {
                var data = res.body;
                this.showlist = data.programList;
                for (var i = 0; i < this.showlist.length; i++) {
                  if (this.showlist[i].status == 1) {
                    var item = this.showlist[i];
                    this.huikan(item.name, item.assetId, item.status, i, item.startTime, item.livePoster);
                    this.statusHave1 = true;
                    break
                  }
                }
                if (this.statusHave1 == true) {
                  this.toast.close();
                } else {
                  this.toast.close();
//             Toast({
//               message: '当前频道无直播!',
//               position: 'middle',
//               duration: 2000
//             });
                }
              }, res => {
                console.log("错误！" + res);
              })

            }, res => {

            })

          } else {
            var l = this.$route.query.liveBroadcastAddress;
            this.$store.commit("videoAddress", l)
          }
        }
      },
      //在点击屏幕开始收集坐标数据
      judge: function (e) {
        this.startX = e.changedTouches[0].clientX;
        this.startY = e.changedTouches[0].clientY;
      },
      //判断滑动方向,模仿点击事件
      toNext: function (e) {
        this.endX = e.changedTouches[0].clientX;
        this.endY = e.changedTouches[0].clientY;
        this.changeX = this.startX - this.endX;
        this.changeY = this.startY - this.endY;
        if ((Math.abs(this.changeX) > 25) && this.changeX > 0 &&
          (Math.abs(this.changeX) > Math.abs(this.changeY))) {
          document.getElementById('comment').click();
        } else if ((Math.abs(this.changeX) > 25) && this.changeX < 0 &&
          Math.abs(this.changeX) > Math.abs(this.changeY)) {
          document.getElementById('channel').click();
        }
      },
      dateInit(){
        var myDate = new Date(); //获取今天日期
        myDate.setDate(myDate.getDate() - 6);
        var dateArray = [];
        var dateTemp;
        var dateinit;
        var flag = 1;
        for (var i = 7; i > 0; i--) {
          dateTemp = (myDate.getMonth() + 1) + "月" + myDate.getDate() + "日";
          dateinit = myDate.getFullYear() + (myDate.getMonth() + 1 < 10 ? "0" + (myDate.getMonth() + 1) : (myDate.getMonth() + 1) + "") + (myDate.getDate() < 10 ? "0" + myDate.getDate() : myDate.getDate());
          dateArray.push({date: dateTemp, dateInit: dateinit});
          myDate.setDate(myDate.getDate() + flag);
        }
        dateArray = dateArray.reverse();
        this.day = dateArray;
      },
      Swiper(){
        new Swiper('aside .swiper-container', {
          slidesPerView: 5,
          spaceBetween: 0,
          direction: 'vertical',
          freeMode: true,
          observer: true
        });
      },
      Swiper2(){
        new Swiper('#content .swiper-container', {
          slidesPerView: 5,
          spaceBetween: 0,
          freeMode: true,
          direction: 'vertical',
          observer: true
        });
      },
      huikan(name, assetId, status, index, startTime, poster) {
        this.$store.commit("huikanTime", startTime);
        switch (Number(status)) {
          case 1:
            this.assetID = assetId;
            this.status = status;
            this.whichTime = startTime;
            var l = this.$route.query.liveBroadcastAddress == undefined ? this.$store.state.videoAddress2 : this.$route.query.liveBroadcastAddress;
//            var l = this.$route.query.liveBroadcastAddress;
            this.$store.commit("videoAddress", this.$store.state.videoAddress2);
            this.isInit = false;
            this.$emit("cb", name, status, poster, assetId);
            this.isClick = index;
            this.clickOnce = true;
            break
          case 2:
            if (assetId != undefined) {
              this.assetID = assetId;
              var u = "http://dp0.sz96296.com:7079/vod/" + this.assetID + "?format=m3u8&targetType=mobile&df=sd&busiType=cpg";
              this.$store.commit("videoAddress", u);
            }
            this.status = status;
            this.whichTime = startTime;
//           if(!this.isInit){

//           }
            this.isInit = false;
            this.$emit("cb", name, status, poster, assetId);
            this.$store.commit("videoAddress", this.$store.state.videoAddress);
            this.isClick = index;
            this.clickOnce = true;
            break
        }
      },
    },
    filters: {
      timeInit(v1){
        return v1.substring(9);
      },
      statusJudge(v1){
        if (v1 == 0) {
          return "预告";
        } else if (v1 == 1) {
          return "直播中";
        } else if (v1 == 2) {
          return "回看";
        }
      },
      color(v1, v2, v3, v4, v5){
        if (v3 == false) {
          if (v2 == 0) {
            return "h1state2";
          } else if (v2 == 1) {
            return "";
          } else if (v2 == 2) {
            return "h1state3";
          }
          return v2 == 0 ? v1 : "";
        } else {
          if (v2 == 0) {
            return "h1state2"
          } else {
            if (v4 == v5) {
              return "";
            } else {
              return "h1state3";
            }
          }

        }
      },
      color2(v1, v2, v3, v4, v5){
//        return v2 == 0 ? v1 + " state2" : v1;
        if (v3 == false) {
          if (v2 == 0) {
            return "state2";
          } else if (v2 == 1) {
            return "state";
          } else if (v2 == 2) {
            return "state3";
          }
        } else {
          if (v2 == 0) {
            return "state2"
          } else {
            return v4 == v5 ? "state" : "state3";
          }
        }
      },
      isClick(v1, v2, v3) {
        return v2 === v3 ? v1 + " isClick" : v1;
      }
    }
  };

</script>

<style scoped>
  #channel_show {
    width: 100%;
    height: 40vh;
    margin-top: 49.5vh;
    overflow: hidden;
  }

  .clearfixed {
    height: 255px;
  }

  /*左边导航  */
  aside {
    border-right: 2px solid #eee;
    width: 25%;
    height: 100%;
    float: left;
    transform: translateY(-0.75vh);
    /*height: 50vh;*/
    /*margin-top: 3vh;*/
  }

  .clear {
    clear: both;
  }

  aside .swiper-container {
    height: 100%;
  }

  aside .swiper-container .every_swiper {
    /*height: 49px;*/
    text-align: center;
    line-height: 55px;
    font-size: 14px;
    font-family: "Microsoft YaHei";
    border-bottom: 1px solid #eee;
    font-weight: bold;
  }

  .dash {
    width: 2px;
    background-color: #FF3E05;
    height: 25px;
    position: absolute;
    top: 8px;
    right: 0px;
  }

  .active {
    font-size: 15px;
    color: #FF3E05;
  }

  /*右边的content*/
  #content {
    width: 74%;
    height: 100%;
    float: right;
    /*height: 50vh;*/
    margin-top: 0vh;
  }

  #content .swiper-container {
    height: 100%;
  }

  h1 {
    /*margin:18px 0 17px 16px;*/
    padding-left: 5px;
    width: 50vw;
    height: 100%;
    /*min-height: 50px;*/
    font-size: 14px;
    line-height: 36px;
    font-family: "Microsoft Yahei";
    color: #04B456;
    float: left;
    white-space: nowrap;
    text-overflow: ellipsis;
    overflow: hidden;
  }

  .h1state2 {
    color: #949494;
  }

  .h1state3 {
    color: #000;
  }

  .state {
    text-align: center;
    width: 14vw;
    height: 25px;
    font-size: 12px;
    color: #04B456;
    float: right;
    border: 1px solid #04B456;
    border-radius: 25px;
    margin: 5px 10px 0 0;
    line-height: 23px;
    background-color: #fff;
    float: right;
  }

  .state2 {
    text-align: center;
    width: 14vw;
    height: 25px;
    font-size: 12px;
    color: #949494;
    float: right;
    border: 1px solid #949494;
    border-radius: 25px;
    margin: 5px 10px 0 0;
    line-height: 23px;
    background-color: #fff;
    float: right;
  }

  .state3 {
    text-align: center;
    width: 14vw;
    height: 25px;
    font-size: 12px;
    color: #000000;
    float: right;
    border: 1px solid #000000;
    border-radius: 25px;
    margin: 5px 10px 0 0;
    line-height: 23px;
    background-color: #fff;
    float: right;
  }

  .isClick {
    color: #04B456 !important;
    border: #04B456 !important;
  }
</style>

