<template>
  <div class="templ">

    <header>
      我的
    </header>

    <figure v-if="$store.state.openid==undefined">
      <img :src="headimg"/>
      <hgroup>
        <h1>未登录</h1>
      </hgroup>
      <!--<span class="icon-chs_sywx_ic_my_jt"></span>-->
    </figure>
    <figure v-else-if="$store.state.openid==0">
      <img :src="headimg"/>
      <hgroup>
        <h1>未登录</h1>
      </hgroup>
      <!--<span class="icon-chs_sywx_ic_my_jt"></span>-->
    </figure>
    <figure v-else-if="$store.state.openid==-1">
      <img :src="headimg"/>
      <hgroup>
        <h1>未登录</h1>
      </hgroup>
      <!--<span class="icon-chs_sywx_ic_my_jt"></span>-->
    </figure>
    <figure v-else>
      <img v-if="$store.state.headImgMsmp==''||'default.jpg'" :src="$store.state.headImg" @click=""/>
      <img v-else :src="$store.state.headImgMsmp" @click=""/>
      <hgroup>
        <h1 v-if="$store.state.nickNameMsmp==''" @click="nicknameState($store.state.nickName);">{{ $store.state.nickName }}</h1>
        <h1 v-else @click="nicknameState($store.state.nickNameMsmp);">{{ $store.state.nickNameMsmp }}</h1>
        <!--<input type="text" v-model="nickName" class="nicknameState2">-->
      </hgroup>
      <!--<span class="icon-chs_sywx_ic_my_jt"></span>-->
    </figure>

    <!--{{ test }}-->
    <!--{{ serverId }}-->
    <!--<div id="xiazai" @click="xiazai">下载</div>-->
    <section>

      <!--在整个div上绑定跳转-->
      <router-link to="/record/recordzb">
        <div class="history">
          <span class="icon-chs_sywx_ic_my_history"></span>
          <h1>观看记录</h1>
          <span class="icon-chs_sywx_ic_my_jt second"></span>
        </div>
      </router-link>


      <div class="collect" @click="goCollection">
        <span class="icon-chs_sywx_ic_my_coll"></span>
        <h1>我的收藏</h1>
        <span class="icon-chs_sywx_ic_my_jt second"></span>
      </div>

      <!--<div class="help">-->
        <!--<span class="icon-chs_sywx_ic_my_help"></span>-->
        <!--<h1>我的反馈</h1>-->
        <!--<span class="icon-chs_sywx_ic_my_jt second"></span>-->
      <!--</div>-->


      <div class="bangding" @click="goSTBlist">
        <span class="icon-chs_sywx_ic_my_box"></span>
        <h1>绑定机顶盒</h1>
        <span class="icon-chs_sywx_ic_my_jt second"></span>
      </div>

      <!--<div class="setting">-->
        <!--<span class="icon-chs_sywx_ic_my_setting"></span>-->
        <!--<h1>设置</h1>-->
        <!--<span class="icon-chs_sywx_ic_my_jt second"></span>-->
      <!--</div>-->

    </section>

  </div>
</template>

<script>
  import defultHeadimg from '../../assets/images/defultHeadimg.png'
  import wx from 'weixin-js-sdk'
  import '@/assets/js/md5.min'
  import '@/assets/js/utils'
  import { MessageBox } from 'mint-ui'
  import { Toast } from 'mint-ui'

  export default {
    data: function () {
      return {
        headimg: defultHeadimg,
        nickName: "",
        nacknameState: false,
        test: "",
        serverId: []
      }
    },
    created() {

    },
    destroyed() {
      this.$store.commit("isAppShow", false);
    },
    mounted() {
      this.$store.commit("isAppShow", true);
//      this.readyShare2();
    },
    filters: {

    },
    computed: {

    },
    methods: {
      goBack() {
        this.$router.go(-1);
      },
      goSTBlist() {
        if (this.$store.state.openid == 0 || this.$store.state.openid == undefined || this.$store.state.openid == -1) {
          this.$emit("send", "other");
        } else {
          this.$router.push({path: '/STB_list'});
        }
      },
      goCollection() {
        if (this.$store.state.openid == 0 || this.$store.state.openid == undefined || this.$store.state.openid == -1) {
          this.$emit("send", "other");
        } else {
          this.$router.push({path: '/collection'});
        }
      },
      chooseImage() {
        var that = this;
        wx.chooseImage({
          count: 9, // 默认9
          sizeType: ['original', 'compressed'], // 可以指定是原图还是压缩图，默认二者都有
          sourceType: ['album', 'camera'], // 可以指定来源是相册还是相机，默认二者都有
          success: function (res) {
            var localIds = res.localIds; // 返回选定照片的本地ID列表，localId可以作为img标签的src属性显示图片
            that.test = JSON.stringify(res);

            for (let i = 0; i < localIds.length; i++) {
              wx.uploadImage({
                localId: localIds[i], // 需要上传的图片的本地ID，由chooseImage接口获得
                isShowProgressTips: 1, // 默认为1，显示进度提示
                success: function (res) {
                  var serverId = res.serverId; // 返回图片的服务器端ID
                  that.test += "," + JSON.stringify(res);
                  that.serverId.push(serverId);
                }
              });
            }

          }
        });
      },
      changeHeadimg(_img) {
        var msmp_appkey = "76579579da14418ba97b1cbe38366410";
        var params = "userid=" + this.$store.state.userID + "&headimage=" + _img;
        var sign = getSign(params + msmp_appkey);
        var url = "/msmp/nscreen/syncheadimage.action?userid=" + this.$store.state.userID + "&headimage=" + _img + "&sign=" + sign;

        this.$http.get(url, {headers: {Authorization: "Basic " + btoa("screen:screen123")}}).then(res => {
          var data = res.body;

        }, res => {

        })
      },

      nicknameState() {
        MessageBox.prompt(" ", "请输入昵称!").then(({ value, action }) => {
          this.changeNickname(value);
        });
      },
      changeNickname(v) {

        if (v == "" || v == null || v.trim() == "") {
          MessageBox.alert("输入不能为空!", "温馨提示");
          return
        }

        var msmp_appkey = "76579579da14418ba97b1cbe38366410";
        var params = "userid=" + this.$store.state.userID + "&loginpwd=123456&aliasname=" + encodeURI(v) + "&devicetype=7";
        var sign = getSign(params + msmp_appkey);
        var url = "/msmp/nscreen/modifynscreenuserinfo.action?userid=" + this.$store.state.userID + "&loginpwd=123456&aliasname=" + encodeURI(v) + "&devicetype=7&sign=" + sign;

        this.$http.get(url, {headers: {Authorization: "Basic " + btoa("screen:screen123")}}).then(res => {
          var data = res.body;
          var code = data.substring(data.indexOf("<errorcode>") + 11, (data.indexOf("</errorcode>")));   //存储状态码
          if (code == 0) {
            Toast("修改成功!");
            this.checkUserMsg();
          }
        }, res => {
          Toast("请稍候再试!");
        })
      },
      checkUserMsg() {
        if (this.$store.state.openid == 0 || this.$store.state.openid == undefined || this.$store.state.openid == -1) {
          return
        }
        var msmp_appkey = "76579579da14418ba97b1cbe38366410";

        var check_params = "userid=" + this.$store.state.userID + "&devicetype=7&resultformat=JSON";
        var check_sign = getSign(check_params + msmp_appkey);
        var check_url = "/msmp/nscreen/getnscreenuserinfo.action?userid=" + this.$store.state.userID + "&devicetype=7&resultformat=JSON&sign=" + check_sign;

        this.$http.get(check_url, {headers: {Authorization: "Basic " + btoa("screen:screen123")}}).then(res => {
          var data = res.body;
          var nickNameMsmp = data.substring(data.indexOf("<aliasname >") + 12, (data.indexOf("</aliasname >")));   //储存用户昵称
          this.$store.commit("nickNameMsmp", nickNameMsmp);
        }, res => {

        })
      },

      xiazai() {
        var that = this;
        wx.downloadImage({
          serverId: "", // 需要下载的图片的服务器端ID，由uploadImage接口获得
          isShowProgressTips: 1, // 默认为1，显示进度提示
          success: function (res) {
            var localId = res.localId; // 返回图片下载后的本地ID
            that.test += "，" + localId;
          }
        });
//        for (let i = 0; i < that.serverId.length; i++) {
//          wx.downloadImage({
//            serverId: that.serverId[i], // 需要下载的图片的服务器端ID，由uploadImage接口获得
//            isShowProgressTips: 1, // 默认为1，显示进度提示
//            success: function (res) {
//              var localId = res.localId; // 返回图片下载后的本地ID
//              that.test += "，" + localId;
//            }
//          });
//        }
      }
    },
    components: {

    }
  };
</script>

<style scoped>

  #xiazai {
    width: 50px;
    height: 50px;
    background-color: red;
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

  .icon-chs_sywx_ic_jt_left {
    font-size: 20px;
    color: #FF3E05;
    position: absolute;
    left: 16px;
    top: 15px;
  }

  figure {
    background-color: #fff;
    margin: 0;
    height: 77px;
    position: relative;
    margin-bottom: 8px;
  }

  figure img {
    display: block;
    width: 15vw;
    height: 15vw;
    border-radius: 8vw;
    position: relative;
    left: 16px;
    top: 1.5vh;
  }

  hgroup {
    height: 38px;
    position: absolute;
    left: 85px;
    top: 18px;
  }

  .nicknameState2 {
    height: 100%;
    border: 0;
  }

  h1 {
    font-weight: normal;
  }

  hgroup h1 {
    height: 100%;
    line-height: 11vw;
    margin: 0;
    font-size: 15px;
    font-family: "Microsoft YaHei";
    color: #3c3c3c;
  }

  hgroup h1:nth-child(2) {
    padding-top: 8px;
  }

  .icon-chs_sywx_ic_my_jt {
    font-size: 20px;
    color: #d5d5d5;
    position: absolute;
    top: 31px;
    right: 16px;
  }

  .history, .collect, .help, .bangding, .setting {
    position: relative;
    height: 49px;
    border-bottom: 1px solid #eee;
    background-color: #fff;
  }

  .help, .bangding {
    margin-bottom: 8px;
  }

  .second {
    top: 17px;
  }

  .icon-chs_sywx_ic_my_history, .icon-chs_sywx_ic_my_help {
    color: #FDCC7E;
    font-size: 22px;
    display: inline-block;
    padding-top: 18px;
    padding-left: 16px;
  }

  .history h1, .collect h1, .help h1, .bangding h1, .setting h1 {
    margin: 0;
    font-size: 15px;
    color: #3c3c3c;
    font-family: "Microsoft YaHei";
    display: inline-block;
    position: absolute;
    left: 51px;
    top: 17px;
  }

  a {
    color: #3c3c3c;
  }

  .icon-chs_sywx_ic_my_coll {
    color: #FF6677;
    font-size: 22px;
    display: inline-block;
    padding-top: 18px;
    padding-left: 16px;
  }

  .icon-chs_sywx_ic_my_box {
    color: #69E0A1;
    font-size: 22px;
    display: inline-block;
    padding-top: 18px;
    padding-left: 16px;
  }

  .icon-chs_sywx_ic_my_setting {
    color: #71CFF3;
    font-size: 22px;
    display: inline-block;
    padding-top: 18px;
    padding-left: 16px;
  }
</style>

