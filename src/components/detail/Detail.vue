<template>
  <div class="templ">
    <header>
      <p>详情</p>
      <span @click="goback" class="icon-chs_sywx_ic_jt_left"></span>
      <!--有分享菜单-->
      <!--<span class="icon-chs_sywx_ic_detail_share"></span>-->
      <!--收藏-->
      <span class="icon-chs_sywx_ic_detail_like" @click="addCollection"></span>
    </header>

    <div class="topHeight"></div>

    <video id="video" controls autoplay x5-video-player-type="h5" v-if="videoMsg.mobileScreen==1" v-show="isMobilePlay" @pause="setRecord">
      <source type="video/mp4" :src="videoChange">
    </video>

    <!--影片详情-->
    <div id="detail">
      <div id="zhezhao" v-show="isMobilePlay"></div>
      <figure v-show="!isMobilePlay">
        <img :src="'/cms/res/posters/mobile/' + videoMsg.posterUrl"/>
        <figcaption>
          <h1>{{ videoMsg.name | isEmpty }}</h1>
          <h2 class="directors" @click="goSearch(videoMsg.directors)">导演：{{ videoMsg.directors | isEmpty }}</h2>
          <h2>年份：{{ videoMsg.year | isEmpty }}</h2>
          <h2>地区：{{ videoMsg.region| isEmpty }}</h2>
          <h2>类型：{{ videoMsg.genre | isEmpty }}</h2>
          <button id="forTv" @click="TVplay(); openApp();">
            <span class="icon-chs_sywx_ic_detail_tp"></span>
            电视观看
          </button>
          <button id="forPhone" v-show="videoMsg.mobileScreen==1" @click="mobilePlay();">
            <span class="icon-chs_sywx_ic_detail_phone"></span>
            手机观看
          </button>
        </figcaption>
      </figure>
    </div>

    <!--观看超时提示-->
    <div id="download" v-show="downloadTips">
      <div id="download_tips">
        <div class="msg">
          在苏州云媒体APP中继续观看？
        </div>
        <span class="lineX"></span>
        <span class="lineY"></span>
        <p id="yes" @click="goDownLoad();">打开</p>
        <p id="no" @click="no();">取消</p>
      </div>
    </div>

    <!--简介-->
    <div id="intro">
      <h1>
        简介
        <div class="leftBorder"></div>
        <!--<span class="icon-chs_sywx_ic_my_jt"></span>-->
      </h1>
      <div class="label">
        <!--v-for循环-->
        <button v-for="item in videoMsg.actors" @click="goSearch(item);">{{ item | isEmpty }}</button>
      </div>
      <p>{{ videoMsg.introduction }}</p>
    </div>

    <!--剧集-->
    <div id="episode" v-show="isMovie!=='movie'">
      <h1>
        剧集
        <div class="leftBorder"></div>
      </h1>
      <div :class="moreCss">
        <button v-for="(n, i) in episode" :class="{butActive:but_index==i, button1:isMovie!='package-title'}"
                @click="changeJujiID(n.id, i); initPackagePhonePlay(n.id); mobilePlay();">

          <span v-if="isZongyi">{{ n.name }}</span>
          <span v-else>{{ n.chaper }}</span>
        </button>
      </div>
      <p id="more" @click="aaa();">{{ isMore }}</p>
    </div>

    <!--推荐-->
    <div id="recommend">
      <h1>
        为您推荐
        <div class="leftBorder"></div>
        <button @click="getRecommend();">换一批</button>
      </h1>
      <!--v-for循环-->
      <div class="recommend-content">
        <figure v-for="(item,index) in recommend" :class="{threeRecommend:index==0||index==1||index==2}"
                @click="goContent(item.id, item.showType); goTop();">
          <img :src="'/cms/res/posters/mobile/' + item.posterUrl"/>
          <h2>{{ item.name }}</h2>
        </figure>
      </div>
    </div>
    <!--&lt;!&ndash;评论&ndash;&gt;-->
    <!--<comment :comments="comments" @updateCom="getComment"></comment>-->

    <!--&lt;!&ndash;评论结束&ndash;&gt;-->
    <!--<div id="end">-->
      <!--<p @click="getNextComment">{{ commentTips }}</p>-->
    <!--</div>-->

    <img class="godownload" src="../../assets/images/lianjie.png" alt="" @click="goDownLoad();">
    <!--<div id="footerHeight"></div>-->

  </div>
</template>

<script>

  import {Toast} from 'mint-ui';
  import {MessageBox} from 'mint-ui'
  import '@/assets/js/strophe'
  import '@/assets/js/strophe.muc'
  import '@/assets/js/strophe.register'
  import wx from 'weixin-js-sdk'
  import defultImg from '../../assets/images/defultHeadimg.png'
  import comment from '../live/Comment.vue'
  import Share from '../../service/share'

  export default {
    data: function () {
      return {
        type: "",
        episode: [],
        recommend: [],
        isMore: "",
        moreCss: "number2",
        comments: [],
        comments_total: 0,
        detallComment: "",     //评论的内容
        totalPage: 0,
        pageNum: 1,

        videoMsg: {},
        isMovie1: "movie",
        isMovie2: this.$route.query.type == "vpg_video" || this.$route.query.type == "local_vpg_video" ? "movie" : this.$route.query.type,
        isMovie: "movie",
        commentTips: "",
        isZongyi: false,
        button: "",
        id: undefined,     //当前电影的id
        name: "",          //当前电影的名字
        toast: null,
        server: 'dp0.sz96296.com',
        BOSH_SERVICE: 'ws://122.193.8.100:7076/websocket',
        jujiID: "",
        but_index: 0,
        isMobilePlay: false,    //当前电影是否播放
        movieURL: "",           //电影地址
        downloadTips: false,      //控制超时提示显示
      }
    },
    components: {
      comment
    },
    filters: {
      areaJduge(v1) {
        if (v1 == 1) {
          return "大陆";
        }
        else if (v1 == 2) {
          return "港澳台";
        }
        else if (v1 == 3) {
          return "欧美";
        }
        else if (v1 == 4) {
          return "日韩";
        }
        else if (v1 == 5) {
          return "东南亚";
        }
        else if (v1 == 6) {
          return "其他";
        }
        else if (v1 == "") {
          return "暂无"
        }
      },
      isEmpty(v1) {
        if (v1 == "" || v1 == "无") {
          return "暂无";
        } else {
          return v1;
        }
      },
      dateInit(v1) {
        var str = v1.replace("T", " ");
        return str.substring(0, str.indexOf("+"));
      },
      headimg(v1, v2) {
        if (v2.slice(0, 4) == "http") {
          return v2;
        }
        else if (v2 == "default.jpg") {
          return defultImg
        }
        else {
          return v2 == "" ? defultImg : v1 + v2;
        }
      }
    },
    computed: {
      sendComment() {
        return {
          'sendComment': this.detallComment.length == 0,
          'sendComment sendComment1': this.detallComment.length == 1,
          'sendComment sendComment2': this.detallComment.length == 2,
          'sendComment sendComment3': this.detallComment.length == 3,
          'sendComment sendComment4': this.detallComment.length == 4,
          'sendComment sendComment5': this.detallComment.length == 5,
          'sendComment sendComment6': this.detallComment.length == 6,
          'sendComment sendComment7': this.detallComment.length == 7,
          'sendComment sendComment8': this.detallComment.length == 8,
          'sendComment sendComment9': this.detallComment.length == 9,
          'sendComment sendComment10': this.detallComment.length >= 10,
        }
      },
      videoChange(){
        return this.isMobilePlay == false ? "" : this.movieURL;
      }
    },
    created(){
      this.getComment();
      this.getVideoDetail();
      this.getRecommend();
//      this.xmpp();
      if (this.$route.query.from !== undefined) {
        this.$store.commit("isSmallRCshow", false);
      }
    },
    mounted(){
      this.aaa();
      this.goTop();
    },
    updated() {
      this.readyShare();
    },
    destroyed() {
      this.readyShare2();
    },
    watch: {
      "$route": "routeFun",
      "$route.query.id": function () {
        this.isMobilePlay = false;
        this.pageNum = 1;
      },
      videoChange() {
        window.setTimeout(function () {
          document.getElementById("video").load();
        }, 10)
      }
    },
    methods: {
      handleBottomChange(Status) {
        this.bottomStatus = Status;
      },
      goback() {
        this.$router.go(-1);
      },
      getVideoDetail(){

        var id = this.$route.query.id,
          url = "/cms/thirdPartyPortalInterfaceV2/getVideoDetail.service?id=" + id,
          actors = [];

        this.$http.get(url).then(res => {
            var data = res.body;
            this.isMovie = data.video.showType;
            this.videoMsg = data.video;
            this.id = data.video.id;
            this.name = data.video.name;
            actors = data.video.actors.split(/、|,|，|\/|\s/);
            this.videoMsg.actors = actors;
            for (var i = 0; i < this.videoMsg.actors.length; i++) {
              if (this.videoMsg.actors[i] == "") {
                this.videoMsg.actors.splice(i, 1);
                i = i - 1;
              }
            }
            this.getJuji();

            this.readyShare1();

            if (this.isMovie == "movie") {
              this.initMoviePhonePlay();
            }
          },
          res => {
            console.log("错误" + res);
          })
      },
      getRecommend(){
        this.toast = Toast({
          message: '加载中...',
          position: 'middle',
          duration: -1
        });

        var id = this.$route.query.id;
        var type;

        if (this.$route.query.showType == "movie") {
          type = "movie";
        } else {
          type = "package";
        }

        var url2 = "/cms/thirdPartyPortalInterfaceV2/listRecommend.service?contentId=" + id + "&size=4" + "&type=" + type;

        this.$http.get(url2).then(res => {
            var data = res.body;
//            console.log(data);
            this.recommend = data.contentList;
            if (this.$route.query.isZhgq == "zhgq") {
              for (var i = 0; i < this.recommend.length; i++) {
                this.recommend[i].aaa = "zhgq";
              }
            }
            this.toast.close();
          },
          res => {
            this.toast.close();
            console.log("错误" + res);
          })
        this.toast.close();
      },
      getJuji(){
        if (this.isMovie != "movie") {
          var id = this.$route.query.id,
            url = "/cms/thirdPartyPortalInterfaceV2/listVideo.service?sortBy=chapter&scope=direct&pageSize=100&categoryId=" + id;

          if (this.isMovie == "package-title") {
            this.isZongyi = true;
            this.button = "";
          }

          this.$http.get(url).then(res => {
              var data = res.body
              if (this.isMovie == "package-title") {
                this.isMore = "更多↓";
              } else {
                this.button = "button1";
              }

              if (data.videoList.length > 10) {
                this.isMore = "更多↓";
              }
              this.episode = data.videoList;
              this.jujiID = this.episode[0].id;

              if (this.isMovie == "package-chapter") {
                this.initPackagePhonePlay(this.jujiID);
              }

            },
            res => {
              console.log("错误" + res);
            })

        } else {
          return
        }
      },
      changeJujiID(id, index) {
        this.jujiID = id;
        this.but_index = index;
      },
      getComment(){
        var id = this.$route.query.id;
        var url = "/cms/thirdPartyPortalInterfaceV2/listCommentByContent.service?contentId=" + id + "&contentType=vpg&pageSize=5";

        this.$http.get(url).then(res => {
            var data = res.body;
            this.comments = data;
            this.totalPage = data.totalPage;
            if (data.totalNum == 0) {
              this.commentTips = "暂无评论~"
            } else {
              this.commentTips = "点击加载...";
            }
          },
          res => {
            console.log("错误" + res);
          })
      },
      getNextComment() {
        if (this.pageNum >= this.totalPage) {
          return
        }

        this.toast = Toast({
          message: '评论加载中...',
          position: 'middle',
          duration: -1
        });

        var id = this.$route.query.id;
        var contentType = "vpg";

        this.pageNum += 1;
        var url = "/cms/thirdPartyPortalInterfaceV2/listCommentByContent.service?contentId=" + id + "&contentType=" + contentType + "&pageSize=5&pageNum=" + this.pageNum;

        this.$http.get(url).then(res => {
            var data = res.body;

            this.comments.commentList = this.comments.commentList.concat(data.commentList);
            this.totalPage = data.totalPage;

            if (data.currentPage == data.totalPage) {
              this.commentTips = "评论看完了，来一发吧!";
            }
            this.toast.close();
          },
          res => {
            this.toast.close();
            Toast("网络拥堵，稍后再试！");
            this.pageNum -= 1;
          })
      },
      aaa(){
        if (this.isMore == "更多↓") {
          this.moreCss = "number1";
          this.isMore = "收回↑";
        }
        else if (this.isMore == "收回↑") {
          this.isMore = "更多↓"
          this.moreCss = "number2";
        }
      },
      aaa2(){
        if (this.isMore == "收回↑") {
          this.isMore = "更多↓"
          this.moreCss = "number2";
        } else if (this.isMore == "更多↓") {
          return
        }
      },
      goTop(){
        document.body.scrollTop = 0;
      },
      setTime(v1){
        return this.$moment(v1, "YYYYMMDD, h:mm:ss").format();
      },
      goDownLoad() {
        window.location.href = "http://dp2.sz96296.com:8091/download/index.html";
      },
      goSearch(_key) {
        if (_key == "" || _key == "无") {
          return
        }
        this.$router.push({path: 'SearchResult', query: {key: _key}});
      },
      goContent(id, showType){
        this.$router.push({path: 'detail', query: {id: id, showType: showType}});
      },
      routeFun(){
        this.getVideoDetail();
        this.getRecommend();
        this.getJuji();
        this.getComment();
        this.aaa2();
      },
      addCollection() {
        //先判断有没有登录
        if (this.$store.state.openid == 0 || this.$store.state.openid == -1 || this.$store.state.openid == undefined) {
          alert("请先登录!");
          return
        }

        //收藏
        var url = '/cms/thirdPartyPortalInterfaceV2/addCollection.service?id=' + this.id + '&videoName=' + this.name + '&clientId=' + this.$store.state.openid + '&terminalType=mobile&videoType=vpg'

        this.$http.get(url).then(res => {
          var data = res.body;
          if (data.code == 0) {
            Toast({
              message: '收藏成功!',
              position: 'middle',
              duration: 1000
            })
          }
          else if (data.code == 97) {
            Toast({
              message: '已收藏过啦!',
              position: 'middle',
              duration: 1000
            })
          }

        }, res => {
          alert("收藏失败，请稍候再试！");
        })
      },
      TVplay() {
        if (this.$route.query.from == undefined) {
          //剧集类投屏
          if (this.isMovie == "vpg_package") {
            this.$emit("send", "package", {ID: this.jujiID});
          }
          else if (this.isMovie == "package-title") {
            this.$emit("send", "package", {ID: this.jujiID});
          }
          else if (this.isMovie == "package-chapter") {
            this.$emit("send", "package", {ID: this.jujiID});
          }
          else if (this.isMovie == "package") {
            this.$emit("send", "package", {ID: this.jujiID});
          }
          //电影类投屏
          else if (this.isMovie == "movie") {
            this.$emit("send", "movie", {ID: this.$route.query.id});
          }
          else if (this.isMovie == "vpg_video") {
            this.$emit("send", "movie", {ID: this.$route.query.id});
          }
          else if (this.isMovie == "local_vpg_video") {
            this.$emit("send", "movie", {ID: this.$route.query.id});
          }
        } else {
          return
        }
      },
      initMoviePhonePlay() {
        if (this.videoMsg.mobileScreen == 1) {
          var userid = this.$store.state.userID == "" ? 0 : this.$store.state.userID;
          var url = '/cms/thirdPartyPortalInterface/play.service?clientId=' + userid + '&terminalType=mobile&busiType=vpg&assetId=' + this.$route.query.id + '&resFormat=json';

          this.$http.get(url).then(res => {
            var data = res.body;

            this.movieURL = data.playResponse.contentList.content[0].url;

          }, res => {

          })
        }

      },
      initPackagePhonePlay(_jujiID) {
        if (this.videoMsg.mobileScreen == 1) {
          var userid = this.$store.state.userID == "" ? 0 : this.$store.state.userID;
          var url = '/cms/thirdPartyPortalInterface/play.service?clientId=' + userid + '&terminalType=mobile&busiType=vpg&assetId=' + _jujiID + '&resFormat=json';

          this.$http.get(url).then(res => {
            var data = res.body;

            this.movieURL = data.playResponse.contentList.content[0].url;

          }, res => {

          })
        }

      },
      mobilePlay() {
        if (this.videoMsg.mobileScreen == 0) {
          return
        }
        this.isMobilePlay = true;
        var video = document.getElementById("video");
        video.ontimeupdate = () => {
          if (video.currentTime > 360) {
            video.pause();
            this.downloadTips = true;
          }
        };
      },
      no() {
        this.downloadTips = false;
      },
      xmpp() {
        if (this.$store.state.connection == null) {
          if (this.$store.state.openid == 0 || this.$store.state.openid == undefined || this.$store.state.openid == -1) {
            return
          } else if (this.$store.state.STBid == "") {
            return
          }

          this.toast = Toast("连接中,给我几秒,请稍等...");
          var that = this;
          var url = this.BOSH_SERVICE;
          this.$store.commit("connection", new Strophe.Connection(url));
          var username = this.$store.state.openid;
          var password = "123456";

          var registerCallback = function (status) {
            if (status === Strophe.Status.REGISTER) {
              that.$store.state.connection.register.fields.username = username;
              that.$store.state.connection.register.fields.password = password;
              that.$store.state.connection.register.submit();
            } else if (status === Strophe.Status.REGISTERED) {
              that.$store.state.connection.authenticate();
            } else if (status === Strophe.Status.CONNECTED) {
              that.toast.close();
              Toast({
                message: '连接成功!',
                duration: 1000
              });
            } else if (status === Strophe.Status.ERROR) {
              that.toast.close();
              Toast({
                message: '连接发生错误!',
                duration: 1000
              });
            } else if (status === Strophe.Status.CONNTIMEOUT) {
              that.toast.close();
              Toast({
                message: '连接超时!',
                duration: 1000
              });
            }
          }
          that.$store.state.connection.register.connect(this.server, registerCallback);
        }
      },
      sendMsg(msg) {

        var to = this.$store.state.STBid + "@" + this.server;
        var from = this.$store.state.openid + "@" + this.server;
        var con = "";

        if (msg == "KEY_QUIT") {
          con = '<Keyboard><InstanceID>0</InstanceID><Unit>KEYBOARD_CODE</Unit><Target>-16777202</Target></Keyboard>';
        }
        else if (msg == "KEY_MENU") {
          con = '<Keyboard><InstanceID>0</InstanceID><Unit>KEYBOARD_CODE</Unit><Target>-16777203</Target></Keyboard>';
        }
        else if (msg == "KEY_VOLUME_DOWN") {
          con = '<Keyboard><InstanceID>0</InstanceID><Unit>KEYBOARD_CODE</Unit><Target>-16777188</Target></Keyboard>';
        }
        else if (msg == "KEY_VOLUME_UP") {
          con = '<Keyboard><InstanceID>0</InstanceID><Unit>KEYBOARD_CODE</Unit><Target>-16777189</Target></Keyboard>';
        }
        else if (msg == "KEY_UP") {
          con = '<Keyboard><InstanceID>0</InstanceID><Unit>KEYBOARD_CODE</Unit><Target>-16777197</Target></Keyboard>';
        }
        else if (msg == "KEY_DOWN") {
          con = '<Keyboard><InstanceID>0</InstanceID><Unit>KEYBOARD_CODE</Unit><Target>-16777196</Target></Keyboard>';
        }
        else if (msg == "KEY_LEFT") {
          con = '<Keyboard><InstanceID>0</InstanceID><Unit>KEYBOARD_CODE</Unit><Target>-16777198</Target></Keyboard>';
        }
        else if (msg == "KEY_RIGHT") {
          con = '<Keyboard><InstanceID>0</InstanceID><Unit>KEYBOARD_CODE</Unit><Target>-16777195</Target></Keyboard>';
        }
        else if (msg == "KEY_OK") {
          con = '<Keyboard><InstanceID>0</InstanceID><Unit>KEYBOARD_CODE</Unit><Target>-16777199</Target></Keyboard>';
        }
        else if (msg == "KEY_POWER") {
          con = '<Keyboard><InstanceID>0</InstanceID><Unit>KEYBOARD_CODE</Unit><Target>-16777215</Target></Keyboard>';
        }
        else if (msg == "KEY_BACK") {
          con = '<Keyboard><InstanceID>0</InstanceID><Unit>KEYBOARD_CODE</Unit><Target>-16777201</Target></Keyboard>';
        }
        else if (msg == "0") {
          con = '<Keyboard><InstanceID>0</InstanceID><Unit>KEYBOARD_CODE</Unit><Target>-16777204</Target></Keyboard>';
        }
        else if (msg == "1") {
          con = '<Keyboard><InstanceID>0</InstanceID><Unit>KEYBOARD_CODE</Unit><Target>-16777213</Target></Keyboard>';
        }
        else if (msg == "2") {
          con = '<Keyboard><InstanceID>0</InstanceID><Unit>KEYBOARD_CODE</Unit><Target>-16777212</Target></Keyboard>';
        }
        else if (msg == "3") {
          con = '<Keyboard><InstanceID>0</InstanceID><Unit>KEYBOARD_CODE</Unit><Target>-16777211</Target></Keyboard>';
        }
        else if (msg == "4") {
          con = '<Keyboard><InstanceID>0</InstanceID><Unit>KEYBOARD_CODE</Unit><Target>-16777210</Target></Keyboard>';
        }
        else if (msg == "5") {
          con = '<Keyboard><InstanceID>0</InstanceID><Unit>KEYBOARD_CODE</Unit><Target>-16777209</Target></Keyboard>';
        }
        else if (msg == "6") {
          con = '<Keyboard><InstanceID>0</InstanceID><Unit>KEYBOARD_CODE</Unit><Target>-16777208</Target></Keyboard>';
        }
        else if (msg == "7") {
          con = '<Keyboard><InstanceID>0</InstanceID><Unit>KEYBOARD_CODE</Unit><Target>-16777207</Target></Keyboard>';
        }
        else if (msg == "8") {
          con = '<Keyboard><InstanceID>0</InstanceID><Unit>KEYBOARD_CODE</Unit><Target>-16777206</Target></Keyboard>';
        }
        else if (msg == "9") {
          con = '<Keyboard><InstanceID>0</InstanceID><Unit>KEYBOARD_CODE</Unit><Target>-16777205</Target></Keyboard>';
        }
        else if (msg == "直播") {
          con = '<SetAVTransportURI><InstanceID>0</InstanceID><CurrentURI></CurrentURI><CurrentURIMetaData>mediaType=3&Freq=' + this.$route.query.frequency + '&Symb=' + this.$route.query.symbolRate + '&Mod=' + this.$route.query.qam + '&ServiceID=' + this.$route.query.serviceId + '&RangeStart=0&RangeEnd=0&Volume=30&PlayOffset=0</CurrentURIMetaData></SetAVTransportURI>';
        }
        else if (msg == "回看") {
          return
          con = '<SetAVTransportURI><InstanceID>0</InstanceID><CurrentURI>http://172.18.224.25/cpg/programtimelineindex.action?clientid=' + this.$store.state.openid + '&groupid=5121&language=&channelid=' + this.$refs.show.channelId + '&channelcategoryid=61,63&isInside=YES&focus_y_num=1&focus_x_num=1&pageNum=8</CurrentURI><CurrentURIMetaData>mediaType=4&Freq=0&Symb=0&Mod=0&ServiceID=0&RangeStart=0&RangeEnd=0&Volume=30&PlayOffset=20</CurrentURIMetaData></SetAVTransportURI>';
        }
        else if (msg == "电影点播") {
          con = '<SetAVTransportURI><InstanceID>0</InstanceID><CurrentURI>http://172.18.224.25/vpg/forsearchindex.do?appid=mobile&hd=y&movieassetid=' + this.$route.query.id + '</CurrentURI><CurrentURIMetaData>mediaType=4&Freq=0&Symb=0&Mod=0&ServiceID=0&RangeStart=0&RangeEnd=0&Volume=30&PlayOffset=48</CurrentURIMetaData></SetAVTransportURI>';
        }
        else if (msg == "剧集点播") {
          con = '<SetAVTransportURI><InstanceID>0</InstanceID><CurrentURI>http://172.18.224.25/vpg/forsearchindex.do?appid=mobile&hd=y&movieassetid=' + this.jujiID + '</CurrentURI><CurrentURIMetaData>mediaType=4&Freq=0&Symb=0&Mod=0&ServiceID=0&RangeStart=0&RangeEnd=0&Volume=30&PlayOffset=48</CurrentURIMetaData></SetAVTransportURI>';
        }

        var m = $msg({
          to: to, //发送过去的地址(机顶盒号id+域名)
          from: from, //本地地址
          type: 'chat'
        }).c("body").t(con);

        this.$store.state.connection.send(m);

      },
      readyShare() {
        var that = this;

        Share.onMenuShareTimeline({
          title: "我正在苏州微信电视看:" + that.name,
          link: window.location.href,
          imgUrl: "http://wxtv1.chsvision.com/cms/res/posters/mobile/" + that.videoMsg.posterUrl,
          success: function () {

          },
          cancel: function () {

          }
        });

        Share.onMenuShareAppMessage({
          title: "我正在苏州微信电视看:" + that.name, // 分享标题
          desc: '快来下在苏州云媒体APP一起观看电影吧！', // 分享描述
          link: window.location.href, // 分享链接，该链接域名或路径必须与当前页面对应的公众号JS安全域名一致
          imgUrl: "http://wxtv1.chsvision.com/cms/res/posters/mobile/" + that.videoMsg.posterUrl, // 分享图标
          type: '', // 分享类型,music、video或link，不填默认为link
          dataUrl: '', // 如果type是music或video，则要提供数据链接，默认为空
          success: function () {
            // 用户确认分享后执行的回调函数
          },
          cancel: function () {
            // 用户取消分享后执行的回调函数
          }
        });

        Share.onMenuShareQQ({
          title: "我正在苏州微信电视看:" + that.name, // 分享标题
          desc: '快来下在苏州云媒体APP一起观看电影吧！', // 分享描述
          link: window.location.href, // 分享链接
          imgUrl: "http://wxtv1.chsvision.com" + that.videoMsg.posterUrl, // 分享图标
          success: function () {
            // 用户确认分享后执行的回调函数
          },
          cancel: function () {
            // 用户取消分享后执行的回调函数
          }
        });

        Share.onMenuShareQZone({
          title: "我正在苏州微信电视看:" + that.name, // 分享标题
          desc: '快来下在苏州云媒体APP一起观看电影吧！', // 分享描述
          link: window.location.href, // 分享链接
          imgUrl: "http://wxtv1.chsvision.com" + that.videoMsg.posterUrl, // 分享图标
          success: function () {
            // 用户确认分享后执行的回调函数
          },
          cancel: function () {
            // 用户取消分享后执行的回调函数
          }
        });
      },
      readyShare1() {
        var that = this;
        wx.onMenuShareTimeline({
          title: "我正在苏州微信电视看:" + that.name,
          link: window.location.href,
          imgUrl: "http://wxtv1.chsvision.com/cms/res/posters/mobile/" + that.videoMsg.posterUrl,
          success: function () {

          },
          cancel: function () {

          }
        });

        wx.onMenuShareAppMessage({
          title: "我正在苏州微信电视看:" + that.name, // 分享标题
          desc: '快来下在苏州云媒体APP一起观看电影吧！', // 分享描述
          link: window.location.href, // 分享链接，该链接域名或路径必须与当前页面对应的公众号JS安全域名一致
          imgUrl: "http://wxtv1.chsvision.com/cms/res/posters/mobile/" + that.videoMsg.posterUrl, // 分享图标
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
          title: "我正在苏州微信电视看:" + that.name, // 分享标题
          desc: '快来下在苏州云媒体APP一起观看电影吧！', // 分享描述
          link: window.location.href, // 分享链接
          imgUrl: "http://wxtv1.chsvision.com/cms/res/posters/mobile/" + that.videoMsg.posterUrl, // 分享图标
          success: function () {
            // 用户确认分享后执行的回调函数
          },
          cancel: function () {
            // 用户取消分享后执行的回调函数
          }
        });

        wx.onMenuShareQZone({
          title: "我正在苏州微信电视看:" + that.name, // 分享标题
          desc: '快来下在苏州云媒体APP一起观看电影吧！', // 分享描述
          link: window.location.href, // 分享链接
          imgUrl: "http://wxtv1.chsvision.com/cms/res/posters/mobile/" + that.videoMsg.posterUrl, // 分享图标
          success: function () {
            // 用户确认分享后执行的回调函数
          },
          cancel: function () {
            // 用户取消分享后执行的回调函数
          }
        });
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
      },
      openApp() {
        if (this.$route.query.from != undefined) {
          var id = this.$route.query.id,
            Chapter = this.$route.query.SubContentChapter;

          if (/android/i.test(navigator.userAgent) || navigator.userAgent.toLowerCase().indexOf("iphone") != -1) {
            var isrefresh = this.$route.query.refresh; // 获得refresh参数
            if (isrefresh == 1) {
              window.location.href = "http://dp2.sz96296.com:8091/download/index.html";
              return
            }
            var host = "suzhou.video";

            var androidUrl_movie = "chssuzhou://" + host + "/message?id=" + id;
            var androidUrl_package = "chssuzhou://" + host + "/message?id=" + id + "&SubContentChapter=" + Chapter;

            var _ios = 'com.dmx.suzhou.iphone://params?id=' + id;

            var _android = Chapter == undefined ? androidUrl_movie : androidUrl_package;

            window.location.href = navigator.userAgent.toLowerCase().indexOf("iphone") != -1 ? _ios : _android;

            if (/android/i.test(navigator.userAgent)) {

              window.setTimeout(function () {
                window.location.href += ( window.location.href.indexOf("?") != -1 ? "&" : "?") + 'refresh=1';
              }, 700);
            } else {
              setTimeout(function () {
                window.location.href = 'itms-apps://itunes.apple.com/cn/app/su-zhou-yun-mei-tiiphone-ban/id859791489?mt=8'
              }, 700);
            }
          } else {
            window.location.href = "http://dp2.sz96296.com:8091/download/index.html";
          }
        }
      },

      setRecord() {
        var v = document.getElementById("video");
        var second = "" + v.currentTime;

        if (second.slice(1, 2) == ".") {
          second = second.slice(0, 1) + "000";
        }
        else {
          if (second.slice(2, 3) == ".") {
            second = second.slice(0, 2) + "000";
          } else if (second.slice(3, 4) == ".") {
            second = second.slice(0, 3) + "000";
          } else if (second.slice(4, 5) == ".") {
            second = second.slice(0, 4) + "000";
          } else if (second.slice(5, 6) == ".") {
            second = second.slice(0, 5) + "000";
          } else if (second.slice(6, 7) == ".") {
            second = second.slice(0, 6) + "000";
          } else if (second.slice(7, 8) == ".") {
            second = second.slice(0, 7) + "000";
          }
        }

        if (second != "0000") {
          var id = this.$route.query.id
          var url = "/cms/thirdPartyPortalInterface/setResumePoint.service?clientId=" + this.$store.state.userID + "&contentId=" + id + "&busiType=vpg&terminalType=mobile&point=" + second + "&resFormat=json";

          this.$http.get(url).then(res => {
            var data = res.body;

          }, res => {

          })
        }
      }
    }
  };

</script>

<style scoped>
  .mint-loadmore {
  }
  .mint-loadmore-bottom span {
    display: inline-block;
    vertical-align: middle;
    transition: all .25s;
  }

  .rotate {
    transform: rotate(180deg);
  }


  .templ {
    /*min-height: 100vh;*/
  }

  #comment {
    margin-top: 8px;
  }

  #footerHeight {
    margin-top: 9vh;
  }

  h1, h2 {
    margin: 0;
    line-height: 100%;
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
  }

  p {
    margin: 0;
  }

  figure {
    margin: 0;
    padding: 0;
  }

  img {
    display: block;
    max-width: 100%;
  }

  /*头部*/
  header {
    background-color: #FF3E05;
    width: 100%;
    height: 45px;
    line-height: 45px;
    position: fixed;
    top: 0;
    z-index: 10;
  }

  header p {
    font-family: "Microsoft Yahei";
    font-size: 17px;
    text-align: center;
    color: #fff;
    /*padding:16px 0 0 0;*/
  }

  .topHeight {
    margin-top: 45px;
  }

  .icon-chs_sywx_ic_jt_left, .icon-chs_sywx_ic_detail_share, .icon-chs_sywx_ic_detail_like {
    font-size: 20px;
    color: #fff;
  }

  .icon-chs_sywx_ic_jt_left {
    position: absolute;
    left: 4.5%;
    top: 14px;
  }

  .icon-chs_sywx_ic_detail_share {
    position: absolute;
    right: 16%;
    top: 14px;
  }

  .icon-chs_sywx_ic_detail_like {
    position: absolute;
    right: 4.5%;
    top: 14px;
  }

  /*视频*/
  #video {
    width: 100%;
    height: 56vw;
    position: fixed;
    top: 45px;
    z-index: 999;
    background-color: #000;
  }

  /* 影片详情 */
  #detail figure {
    background-color: #fff;
    padding: 16px 0 16px 4.5%;
    margin: 0 0 8px 0;
    overflow: hidden;
  }

  #zhezhao {
    width: 100%;
    height: 56vw;
    background-color: #fff;
  }

  #detail img {
    float: left;
    width: 33%;
    /*margin-bottom:;*/
  }

  #detail figcaption {
    width: 58%;
    height: 163px;
    margin-left: 4.5%;
    float: left;
    position: relative;
  }

  #detail figure:after {
    height: 0;
    content: '.';
    display: block;
    clear: both;
    visibility: hidden;
  }

  #detail figcaption h1 {
    height: 20px;
    font-size: 18px;
    line-height: 20px;
    font-family: "Microsoft Yahei";
    color: #FF3E05;
    margin: 0 0 14px 0;
  }

  #detail figcaption h2 {
    height: 14px;
    line-height: 14px;
    font-size: 14px;
    font-family: "Microsoft Yahei";
    color: #999;
    margin-bottom: 8px;
  }

  #detail figcaption h2.directors {
    color: #00C72E;
  }

  #detail figcaption button {
    padding: 0;
    color: #fff;
    width: 45%;
    height: 36px;
    border-radius: 3px;
    border: 0;
    text-align: center;
    font-size: 13px;
    font-family: "Microsoft Yahei";
  }

  #detail figcaption button#forTv {
    background-color: #FF3E05;
    position: absolute;
    bottom: 0;
  }

  #detail figcaption button#forPhone {
    background-color: #00C72E;
    position: absolute;
    bottom: 0;
    left: 50%;
  }

  #detail figcaption button span {
    font-size: 14px;
  }

  /*超时提示*/
  #download {
    width: 100%;
    height: 100vh;
    position: fixed;
    top: 0;
    background: rgba(0, 0, 0, 0.3);
    z-index: 1000;
  }

  #download_tips {
    width: 70vw;
    height: 45vw;
    background-color: #fff;
    position: fixed;
    z-index: 9999;
    top: 50%;
    left: 50%;
    transform: translateX(-35vw) translateY(-55vw);
  }

  #download_tips .msg {
    width: 100%;
    height: 32vw;
    text-align: center;
    line-height: 32vw;
    font-size: 16px;
  }

  #download_tips .lineX {
    display: block;
    width: 100%;
    height: 1px;
    background-color: #c3c3c3;
    position: absolute;
    bottom: 13vw;
  }

  #download_tips .lineY {
    display: block;
    width: 13vw;
    height: 1px;
    background-color: #c3c3c3;
    position: absolute;
    bottom: 6.5vw;
    left: 28.5vw;
    transform: rotate(90deg);
  }

  #download_tips p {
    float: left;
    width: 35vw;
    height: 13vw;
    text-align: center;
    line-height: 13vw;
    position: absolute;
  }

  #yes {
    bottom: 0;
    color: #2da7ff;
  }

  #no {
    bottom: 0;
    right: 0;
    color: red;
  }

  /*简介  */
  /*这边有个bug,去掉边框h1掉下去,所以加个白色边框*/
  #intro {
    border: 1px solid #fff;
    background-color: #fff;
    /*height:127px;*/
    margin: 0 0 8px 0;
  }

  #intro h1 {
    font-size: 16px;
    font-family: "Microsoft Yahei";
    color: #3C3C3C;
    padding-left: 8px;
    margin: 12px 0 0 4.5%;
    position: relative;
  }

  .leftBorder {
    width: 3px;
    height: 16px;
    border-radius: 2px;
    background-color: #FF3E05;
    position: absolute;
    top: 0;
    left: 0;
  }

  .icon-chs_sywx_ic_my_jt {
    font-size: 15px;
    color: #d5d5d5;
    position: absolute;
    top: 0px;
    right: 4.5%;
  }

  #intro .label {
    margin-top: 8px;
    border-top: 1px solid #eee;
    padding-top: 9px;
  }

  #intro .label button {
    border-radius: 0;
    margin: 0 0 2% 2%;
    height: 30px;
    color: #00C72E;
    font-family: "Microsoft Yahei";
    font-size: 14px;
    border: 1px solid #00C72E;
    background-color: #fff;
  }

  #intro p {
    padding: 0 2% 2% 2%;
    color: #979797;
    font-family: "Microsoft Yahei";
    font-size: 14px;
    line-height: 1.3;
  }

  /*剧集  */
  /*高度自适应,由集数决定，每行值显示5集*/
  /*这边有个bug,去掉边框h1掉下去,所以加个白色边框*/
  #episode {
    border: 1px solid #fff;
    background-color: #fff;
    margin: 0 0 8px 0;
  }

  #episode h1 {
    font-size: 16px;
    font-family: "Microsoft Yahei";
    color: #3C3C3C;
    padding-left: 8px;
    margin: 12px 0 0 4.5%;
    position: relative;
  }

  #episode .number1 {
    text-align: center;
    margin-top: 8px;
    border-top: 1px solid #eee;
    padding: 9px 0 0 8.5%;
    overflow: hidden;
  }

  #episode .number2 {
    text-align: center;
    margin-top: 8px;
    border-top: 1px solid #eee;
    padding: 9px 0 0 8.5%;
    height: 75px;
    overflow: hidden;
  }

  #episode button {
    width: 80%;
    padding: 0;
    margin: 0 2% 9px 4.5%;
    border-radius: 0;
    border: 1px solid #b6b6b6;
    background-color: #fff;
    height: 30px;
    /*width: 90%;*/
    float: left;
    overflow: hidden; /*自动隐藏文字*/
    text-overflow: ellipsis; /*文字隐藏后添加省略号*/
    white-space: nowrap; /*强制不换行*/
  }

  .button1 {
    width: 11.1% !important;
  }

  .butActive {
    color: #FF3E05;
    border: 1px solid #FF3E05 !important;
  }

  #episode button span {

  }

  #episode .numberSelect {
    border-color: #FF3E05;
  }

  /*为您推荐  */
  #recommend {
    border: 1px solid #fff;
    background-color: #fff;
    overflow: hidden;
    margin: 0 0 0 0;
    padding-bottom: 8px;
  }

  #recommend h1 {
    height: 18px;
    font-size: 16px;
    font-family: "Microsoft Yahei";
    color: #3C3C3C;
    padding-left: 8px;
    margin: 12px 0 0 4.5%;
    position: relative;
  }

  #recommend h1 button {
    padding: 0;
    width: 51px;
    height: 18px;
    border-radius: 8px;
    font-size: 12px;
    color: #000000;
    font-family: "Microsoft Yahei";
    position: absolute;
    right: 4.5%;
    line-height: 100%;
  }

  #recommend .recommend-content {
    border-top: 1px solid #eee;
    margin: 12px 3% 0 4.5%;
    padding-top: 16px;
  }

  #recommend .recommend-content figure {
    display: inline-block;
    width: 23.3%;

  }

  #recommend .recommend-content h2 {
    height: 16px;
    line-height: 16px;
    font-size: 13px;
    color: #979797;
    font-family: "Microsoft Yahei";
    padding-top: 8px;
    text-align: center;
  }

  /*前三个右边留空*/
  .threeRecommend {
    margin-right: 2.2%;
  }

  /*评论*/
  section {
    background-color: #fff;
  }

  /*评论条数  */
  section .comment {
    position: relative;
    height: 41px;
    font-size: 16px;
    font-family: "Microsoft YaHei";
    color: #3C3C3C;
    line-height: 41px;
    padding-left: 20px;
    border-bottom: 1px solid #eee;
  }

  section .comment #dash {
    width: 4px;
    height: 19px;
    background-color: #FF3E05;
    position: absolute;
    left: 8px;
    top: 11px;
    border-radius: 4px;
  }

  /*输入框*/
  section .input {
    height: 72px;
    text-align: center;
    line-height: 72px;
    border-bottom: 1px solid #eee;
    position: relative;
  }

  section input {
    border: 0;
    background-color: #F3F3F3;
    width: 70vw;
    height: 42px;
    color: #222222;
    font-size: 13px;
    font-family: "Microsoft Yahei";
    text-align: center;
  }

  section .input .sendComment {
    position: absolute;
    display: block;
    width: 15vw;
    height: 42px;
    line-height: 42px;
    top: 15px;
    right: 30px;
    border-radius: 5px;
    border: 1px solid #dddddd;
    background-color: #fff;
    color: #dddddd;
    transition: all 0.3s;
  }

  .sendComment1 {
    color: #c8c8c8 !important;
    border: 1px solid #c8c8c8 !important;
    transition: all 0.3s;
  }

  .sendComment2 {
    color: #b3b3b3 !important;
    border: 1px solid #b3b3b3 !important;
    transition: all 0.3s;
  }

  .sendComment3 {
    color: #9e9e9e !important;
    border: 1px solid #9e9e9e !important;
    transition: all 0.3s;
  }

  .sendComment4 {
    color: #979797 !important;
    border: 1px solid #979797 !important;
    transition: all 0.3s;
  }

  .sendComment5 {
    color: #848484 !important;
    border: 1px solid #848484 !important;
    transition: all 0.3s;
  }

  .sendComment6 {
    color: #6f6f6f !important;
    border: 1px solid #6f6f6f !important;
    transition: all 0.3s;
  }

  .sendComment7 {
    color: #4f4f4f !important;
    border: 1px solid #4f4f4f !important;
    transition: all 0.3s;
  }

  .sendComment8 {
    color: #303030 !important;
    border: 1px solid #303030 !important;
    transition: all 0.3s;
  }

  .sendComment9 {
    color: #000 !important;
    border: 1px solid #000 !important;
    transition: all 0.3s;
  }

  .sendComment10 {
    color: #000 !important;
    transition: all 1.5s !important;
    box-shadow: 0px 0px 5px 1px #000000;
  }

  section input::-webkit-input-placeholder {
    color: #979797;
    font-size: 13px;
    font-family: "Microsoft Yahei";
  }

  section li {
    width: 90%;
    height: 60px;
    margin: 0 auto;
    border-bottom: 1px solid #e1e1e1;
    position: relative;
  }

  section img {
    float: left;
    width: 10vw;
    border-radius: 50%;
    transform: translateY(3vw);
  }

  section h2 {
    height: 14px;
    font-size: 12px;
    line-height: 14px;
    color: #b5b5b5;
    position: absolute;
    top: 10px;
    left: 12vw;
  }

  section p.comCon2 {
    position: absolute;
    top: 25px;
    left: 12vw;
  }

  section span.comFloor {
    position: absolute;
    top: 10px;
    right: 10px;
    color: #b5b5b5;
  }

  section span.icon-chs_sywx_ic_detail_news {
    float: right;
    transform: translateX(-10px) translateY(40px);
    color: #828282;
  }

  section span.comDel {
    float: right;
    transform: translateX(-5px) translateY(38px);
    color: red;
  }

  #end {
    background-color: #fff;
  }

  #end p {
    font-size: 15px;
    font-family: "Microsoft Yahei";
    color: #000;
    text-align: center;
    line-height: 48px;
  }

  #more {
    position: relative;
    right: -78vw;
    color: #FF3E05;
    margin: 4px 0 9px 0;
    display: inline-block;
  }

  .godownload {
    position: relative;
    bottom: 0
  }
</style>
