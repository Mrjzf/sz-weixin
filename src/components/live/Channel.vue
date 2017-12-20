<template>
  <div class="templ">

    <!--<LiveHead :tolivehead="tolivehead"></LiveHead>-->
    <!--<div class="clearfixed"></div>-->
    <!--<router-view></router-view>-->
    <Top></Top>

    <section>

      <div id="leftSide">

        <!-- 这里将index作为参数传到changeActive里去 -->
        <div class="swiper-container">
          <div class="swiper-wrapper">
            <div v-for="(item,index) in classify" class="every_swiper swiper-slide"
                 :class="{active:$store.state.Channel_index==index}"
                 @click="changeActive(index, item.id)">{{item.name}}
              <div class="dash" v-show="$store.state.Channel_index==index"></div>
            </div>
          </div>
        </div>

      </div>


      <div id="rightSide">
        <mt-loadmore :top-method="upDate" @top-status-change="handleTopChange" ref="loadmore">

          <div>
            <div v-for="item in show" class="swiper-slide every_swiper"
                 @click="goShow(item.shengChannelId, item.id, item.name, item.currentProgram.name, item.channelUrlList.channelUrl[3].url, item.params, item.poster)">
              <img :src="item.poster | imgChange"/>
              <figure>
                <!--<img src="../../assets/zjtb.png"/>-->
                <h1>{{item.name}}</h1>
                <h2>{{item.currentProgram.startTime | timeInit}} {{item.currentProgram.name}}</h2>
                <h3>即将播出:</h3>
                <h3>{{item.nextProgram.startTime | timeInit}} {{item.nextProgram.name}}</h3>
              </figure>

              <!--消除浮动的-->
              <div class="clear"></div>
            </div>
          </div>
          <div slot="top" class="mint-loadmore-top">
            <div v-show="topStatus !== 'loading'">
              <span :class="{ 'rotate': topStatus === 'drop' }">↓</span>
              <span v-if="topStatus === 'pull'">&nbsp;下拉刷新</span>
              <span v-else>&nbsp;释放刷新</span>
            </div>
            <span v-show="topStatus === 'loading'">Loading...</span>
          </div>
        </mt-loadmore>

        <!--<div class="swiper-container">-->
        <!--<div class="swiper-wrapper">-->
        <!--<div v-for="item in show" class="swiper-slide every_swiper"-->
        <!--@click="goShow(item.id, item.name, item.currentProgram.name, item.channelUrlList.channelUrl[3].url)">-->
        <!--<img :src="item.poster | imgChange"/>-->
        <!--<figure>-->
        <!--&lt;!&ndash;<img src="../../assets/zjtb.png"/>&ndash;&gt;-->
        <!--<h1>{{item.name}}</h1>-->
        <!--<h2>{{item.currentProgram.startTime | timeInit}} {{item.currentProgram.name}}</h2>-->
        <!--<h3>即将播出:</h3>-->
        <!--<h3>{{item.nextProgram.startTime | timeInit}} {{item.nextProgram.name}}</h3>-->
        <!--</figure>-->

        <!--&lt;!&ndash;消除浮动的&ndash;&gt;-->
        <!--<div class="clear"></div>-->
        <!--</div>-->
        <!--</div>-->
        <!--</div>-->
        <div id="footerHeight"></div>
      </div>
      <div class="clear"></div>

    </section>

  </div>
</template>

<script>

  import LiveHead from './LiveHead.vue'
  import Top from '@/components/All_Public/Top.vue'
  import {Toast} from 'mint-ui';
  import wx from 'weixin-js-sdk'

  export default {
    data: function () {
      return {
        classify: [],
        tolivehead: 1,
        isActive: 0,
        category: "",
        show: [],
        startX: 0,
        endX: 0,
        changeX: 0,
        startY: 0,
        startY: 0,
        changeY: 0,
        topStatus: "",
        toast: null
      }
    },
    components: {
      LiveHead,
      Top
    },
    created(){
      this.toast = Toast("加载中...");
      this.getType();
    },
    updated(){

    },
    mounted(){
      this.Swiper();
      this.$store.commit("isAppShow", true);
      this.$router.push({path: "/live/Channel"});
    },
    destroyed(){
//      this.changeappIndex();
      this.$store.commit("isAppShow", false);
    },
    methods: {
      changeActive: function (index, category) {
        if (this.$store.state.Channel_category == category) {
          return
        }
        this.$store.commit("Channelcategory", category);
        this.$store.commit("Channelindex", index);
        this.isActive = index;
        var url = "/cms/thirdPartyPortalInterface/getNScreenChannelList.service?resFormat=json&category=" + this.$store.state.Channel_category + "&customgroup=normal&terminalType=mobile-live&nsRegionId=2";

        this.$http.get(url).then(res => {
            var data = res.body.getNScreenChannelListResponse.channelList.channel;
            this.show = data;
          },
          res => {
            console.log("错误！" + res);
          })
      },
      upDate() {
        if (this.$store.state.Channel_category == "") {
          this.$store.commit("Channelcategory", 1000);
        }
        var url = "/cms/thirdPartyPortalInterface/getNScreenChannelList.service?resFormat=json&category=" + this.$store.state.Channel_category + "&customgroup=normal&terminalType=mobile-live&nsRegionId=2";

        this.$http.get(url).then(res => {
            var data = res.body.getNScreenChannelListResponse.channelList.channel;
            this.show = data;

            this.$refs.loadmore.onTopLoaded();
          },
          res => {
            console.log("错误！" + res);
          })

      },
      getType(){
//        获取频道分类


        var url = "/cms/thirdPartyPortalInterface/getNScreenChannelCategory.service?resFormat=json";
        this.$http.get(url).then(res => {
            var data = res.body.getNScreenChannelCategoryResponse.categoryList.category;
            this.classify = data;

//            this.readyShare2();
          },
          res => {
            console.log("错误！" + res);
          })


//        获取第一次数据
        if (this.$route.query.whereFrom == "Rec") {
          this.$store.commit("Channelindex", 4);
          var url2 = "/cms/thirdPartyPortalInterface/getNScreenChannelList.service?resFormat=json&category=1004&customgroup=normal&terminalType=mobile-live&nsRegionId=2";
          this.$http.get(url2).then(res => {
              var data = res.body.getNScreenChannelListResponse.channelList.channel;
              this.show = data;
              this.toast.close();

            },
            res => {
              console.log("错误！" + res);
            })
        }
        else {
          if (this.$store.state.Channel_category == "") {
            var category = 1000;
            var url2 = "/cms/thirdPartyPortalInterface/getNScreenChannelList.service?resFormat=json&category=" + category + "&customgroup=normal&terminalType=mobile-live&nsRegionId=2";
            this.$http.get(url2).then(res => {
                var data = res.body.getNScreenChannelListResponse.channelList.channel;
                this.show = data;
                this.toast.close();

              },
              res => {
                console.log("错误！" + res);
              })
          } else {
            var url2 = "/cms/thirdPartyPortalInterface/getNScreenChannelList.service?resFormat=json&category=" + this.$store.state.Channel_category + "&customgroup=normal&terminalType=mobile-live&nsRegionId=2";
            this.$http.get(url2).then(res => {
                var data = res.body.getNScreenChannelListResponse.channelList.channel;
                this.show = data;
                this.toast.close();

              },
              res => {
                console.log("错误！" + res);
              })
          }
        }

        //获取第一个频道评论
//        var url3 = "/cms/thirdPartyPortalInterface/listComment.service?contentType=cpg_channel&resFormat=json&targetType=mobile&contentId=204455";
//        this.$http.get(url3).then(res => {
//            var data = res.body;
//            this.$store.commentList = data.listCommentResponse;
//          },
//          res => {
//            console.log("错误！" + res);
//          })
      },
      Swiper(){
        new Swiper('#leftSide .swiper-container', {
          slidesPerView: 10,
          direction: 'vertical',
          freeMode: true,
          observer: true
        });
      },
      Swiper2(){
        new Swiper('#rightSide .swiper-container', {
          slidesPerView: 6,
          spaceBetween: 0,
          freeMode: true,
          direction: 'vertical',
          setWrapperSize: true,
          observer: true
        });
      },
      goShow(shengchannelId, channelId, name, currentShow, liveBroadcastAddress, params, poster){
//        this.$store.commit("videoAddress", liveBroadcastAddress + "?delay=30");
        this.$router.push({
          path: '/live/liveHead',
          query: {
            shengchannelId: shengchannelId,
            channelId: channelId,
            name: name,
            currentShow: currentShow,
//              liveBroadcastAddress: liveBroadcastAddress,
            bitRate: params.bitRate,
            frequency: params.frequency,
            qam: params.qam,
            serviceId: params.serviceId,
            symbolRate: params.symbolRate,
            poster: poster
          }
        });
      },
      changeappIndex(){
        this.$store.commit("isappIndex", false);
        this.$store.commit("appIndex", 0);
      },
      handleTopChange(status){
        this.topStatus = status
      },
      readyShare2() {
        wx.onMenuShareTimeline({
          title: "我正在看苏州有线微信电视",
          link: "http://wxtv1.sz96296.com/wxtv/#/Recommend",
          imgUrl: 'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1498053089304&di=c16344e7decb8004e7b06eb23eac259a&imgtype=0&src=http%3A%2F%2Fimg.25pp.com%2Fuploadfile%2Fapp%2Ficon%2F20160202%2F1454373232648196.jpg',
          success: function () {

          },
          cancel: function () {
          }
        });

        wx.onMenuShareAppMessage({
          title: '我正在看苏州有线微信电视', // 分享标题
          desc: '快和我一起看苏州有线微信电视吧~~', // 分享描述
          link: 'http://wxtv1.sz96296.com/wxtv/#/Recommend', // 分享链接，该链接域名或路径必须与当前页面对应的公众号JS安全域名一致
          imgUrl: 'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1498053089304&di=c16344e7decb8004e7b06eb23eac259a&imgtype=0&src=http%3A%2F%2Fimg.25pp.com%2Fuploadfile%2Fapp%2Ficon%2F20160202%2F1454373232648196.jpg', // 分享图标
          type: '', // 分享类型,music、video或link，不填默认为link
          dataUrl: '', // 如果type是music或video，则要提供数据链接，默认为空
          success: function () {
            // 用户确认分享后执行的回调函数
          },
          cancel: function () {
            // 用户取消分享后执行的回调函数
          }
        });

        wx.onMenuShareQQ({
          title: '我正在看苏州有线微信电视', // 分享标题
          desc: '快和我一起看苏州有线微信电视吧~~', // 分享描述
          link: 'http://wxtv1.sz96296.com/wxtv/#/Recommend', // 分享链接
          imgUrl: 'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1498053089304&di=c16344e7decb8004e7b06eb23eac259a&imgtype=0&src=http%3A%2F%2Fimg.25pp.com%2Fuploadfile%2Fapp%2Ficon%2F20160202%2F1454373232648196.jpg', // 分享图标
          success: function () {
            // 用户确认分享后执行的回调函数
          },
          cancel: function () {
            // 用户取消分享后执行的回调函数
          }
        });

        wx.onMenuShareQZone({
          title: '我正在看苏州有线微信电视', // 分享标题
          desc: '快和我一起看苏州有线微信电视吧~~', // 分享描述
          link: 'http://wxtv1.sz96296.com/wxtv/#/Recommend', // 分享链接
          imgUrl: 'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1498053089304&di=c16344e7decb8004e7b06eb23eac259a&imgtype=0&src=http%3A%2F%2Fimg.25pp.com%2Fuploadfile%2Fapp%2Ficon%2F20160202%2F1454373232648196.jpg', // 分享图标
          success: function () {
            // 用户确认分享后执行的回调函数
          },
          cancel: function () {
            // 用户取消分享后执行的回调函数
          }
        });
      }
    },
    filters: {
      timeInit(v1){
        return v1.substring(8, 13);
      },
      imgChange(v1){
        var s = Math.random();
        return "/cms/" + v1 + "?s=" + s;
      }
    }
  };

</script>

<style scoped>

  .clearfixed {
    height: 255px;
  }

  section {
    background-color: #fff;
    margin-top: 8vh;
  }

  /*左边的导航*/
  #leftSide {
    position: fixed;
    border-right: 2px solid #eee;
    width: 25%;
    float: left;
  }

  .clear {
    clear: both;
  }

  #leftSide .swiper-container {
    height: 90vh;
  }

  #leftSide .swiper-container .every_swiper {
    list-style: none;
    position: relative;
    /*height: 49px;*/
    text-align: center;
    line-height: 50px;
    font-size: 14px;
    font-family: "Microsoft YaHei";
  }

  .dash {
    width: 4px;
    background-color: #FF3E05;
    height: 25px;
    position: absolute;
    top: 11px;
    right: -2px;
  }

  .active {
    font-size: 15px;
    color: #FF3E05;
  }

  /*右边的#rightSide*/
  #rightSide {
    width: 74%;
    float: right;

  }

  .mint-loadmore {
    min-height: 83.5vh;
  }

  .mint-loadmore-top span {
    display: inline-block;
    vertical-align: middle;
    transition: all .25s;
  }

  .rotate {
    transform: rotate(180deg);
  }

  #rightSide {
    margin-top: 5px;
  }

  #rightSide .swiper-container {
    height: 84vh;;
    /*padding: 2vh 0 0 0;*/
  }

  #rightSide .every_swiper {
    min-height: 75px;
    padding: 2.5px 0 2.5px 0;
  }

  #rightSide .swiper-slide img {
    width: 45%;
    margin-top: 1vw;
    float: left;
  }

  figure {
    width: 52%;
    margin: 0 0 0 2.5%;
    padding: 0;
    float: left;
  }

  h1 {
    font-weight: normal;
    margin: 0;
    /*padding-left: 20px;*/
    font-size: 14px;
    font-family: "Microsoft Yahei";
  }

  h2 {
    font-weight: normal;
    font-size: 12px;
    font-family: "Microsoft Yahei";
    color: red;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }

  /*为什么这里是从3开始*/
  h2:nth-child(3) {
    margin-top: 15px;
    color: #555;
  }

  h2:nth-child(4) {
    margin-top: 10px;
    color: #999;
  }

  h3 {
    font-size: 12px;
    color: #919191;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }

  #footerHeight {
    margin-bottom: 8vh;
  }

</style>

