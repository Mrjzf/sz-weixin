<template>
  <div id="STB">

    <header>
      <span class="icon-chs_sywx_ic_jt_left" @click="goBack"></span>
      <span class="title">机顶盒</span>
      <span class="add" @click="changeConfirmShow();">扫一扫添加</span>
    </header>

    <div id="tips" v-show="isTipsShow">
      <img src="../../assets/images/voice@2x.png" alt="" class="voice">
      <marquee behavior="" direction="left" class="mar">
        在云媒体电视[娱乐]-[云媒体APP]栏目，扫描二维码绑定机顶盒
      </marquee>
      <img src="../../assets/images/close@2x.png" alt="" class="close" @click="changeTipsShow">
    </div>

    <div id="banner">
      <img src="../../assets/images/switch_icon_bigbox.png" alt="">
    </div>

    <div id="STB_connect" v-show="isConfirmShow">
      <div class="con">
        <input type="text" placeholder="请输入绑定机顶盒描述,如：客厅" v-model="STB_name">
        <p class="tips">
          步骤一、打开江苏有线苏州分公司的云媒体机顶盒，进入栏目[娱乐]-[云媒体APP]
          <br>
          <br>
          步骤二、输入机顶盒描述，点击确认，扫描步骤一电视上的二维码即可
        </p>
        <span class="yes" @click="changeConfirmShow(); saoyisao();">确定</span>
        <span class="no" @click="changeConfirmShow();">取消</span>
      </div>
    </div>

    <div id="STB_msg" v-show="isSTBmsgShow">
      <div class="con">
        <div class="title">修改机顶盒信息</div>
        <div class="item">机顶盒号: {{ changeClientid }}</div>
        <div class="item">电视号: {{ changeTVno }}</div>
        <div class="item">机顶盒名: <input v-model="stb_name"></div>
        <span class="yes" @click="changeSTBmsg();">确定</span>
        <span class="no" @click="closeSTBshow();">取消</span>
      </div>
    </div>
    <!--{{ test }}-->
    <mt-loadmore :top-method="chaxun"
                 @top-status-change="handleTopChange"
                 ref="loadmore"
                 v-show="$store.state.isHaveSTB">

      <div>
        <div class="STBs" v-for="(item, index) in STB_List">
          <img src="../../assets/images/switch_icon_box.png" alt="">
          <p class="STB_name" @click="changeSTB_index(index);">{{ item.clientname }}</p>
          <div :class="'Connection_state' | STBactive(index, $store.state.STBactiveIndex)" @click="connect(item.clientid, index, item.clientname);">{{ '未连接' | STBisConnect(index, $store.state.STBactiveIndex) }}</div>
        </div>
      </div>

      <div slot="top" class="mint-loadmore-top">
        <div v-show="topStatus !== 'loading'" >
          <span :class="{'rotate': topStatus === 'drop'}">↓</span>
          <span v-if="topStatus === 'pull'">&nbsp;下拉刷新</span>
          <span v-else>&nbsp;释放刷新</span>
        </div>
        <span v-show="topStatus === 'loading'">Loading...</span>
      </div>

    </mt-loadmore>

    <!--<div class="yuyintu" v-show="is">-->
      <!--识别中-->
    <!--</div>-->

    <!--{{ yuyinres }}-->

    <!--<div id="yuyin" @touchstart.prevent="yuyinan" @touchmove.prevent="aaa" @touchend="yuyinsong">-->
      <!--语音识别-->
    <!--</div>-->

    <img src="../../assets/images/RC/diagram_icon_notbound.png" alt="" class="notConnect" v-show="!$store.state.isHaveSTB">

  </div>
</template>

<script>
  import wx from 'weixin-js-sdk'
  import '@/assets/js/md5.min'
  import '@/assets/js/utils'
  import { Toast } from 'mint-ui';

  export default {
    data(){
      return {
        msg: "",
        name: "XXX的机顶盒",
        STB_List: [],
        STB_List2: 0,
        toast: null,
        topStatus: "",
        isConfirmShow: false,
        STB_name: "",
        isTipsShow: true,
        stb_index: 0,      //点击的哪一个stb
        stb_name: "",
        isSTBmsgShow: false,
        test: "",

        yuyinres: "",
        is: false
      }
    },
    created() {
//      this.weixin();
      this.dengluzhuce();
    },
    mounted() {

    },
    methods: {
      weixin() {
        var url = "/cms/thirdPartyPortalInterface/doWeiXinGetSign.service?url=" + window.location.href;
        this.toast = Toast({
          message: '加载中...',
          position: 'middle',
          duration: -1
        });

        this.$http.get(url).then(res => {
          var data = res.body;
          this.wxConfig(data.appid, data.timestamp, data.nonceStr, data.signature);
        }, res => {
          console.log("错误!");
        })
      },
      wxConfig(_appid, _timestamp, _nonceStr, _signature) {
        wx.config({
          debug: false,
          appId: _appid,
          timestamp: _timestamp,
          nonceStr: _nonceStr,
          signature: _signature,
          jsApiList: [
            'scanQRCode'
          ]
        })

        wx.ready(function (res) {
//          alert("success-ready");
        })

        wx.error(function (res) {
//          alert("请稍后再试！");
        })
      },
      handleTopChange(status){
        this.topStatus = status
      },
      changeConfirmShow() {
        if (this.isConfirmShow == false) {
          this.isConfirmShow = true;
        } else if (this.isConfirmShow == true) {
          this.isConfirmShow = false;
        }
      },
      goBack() {
        this.$router.go(-1);
      },
      connect(_clientid, _index, _clientname) {
        if (_clientname == "") {
          _clientname = "客厅"
        }
        this.$store.commit("STBid", _clientid);
        this.$store.commit("STBactiveIndex", _index);
        this.$store.commit("STBname", _clientname);
      },
      saoyisao() {
        if (this.$store.state.openid == 0 || this.$store.state.openid == undefined || this.$store.state.openid == -1) {
          alert("请先登录!");
          return
        }
        var msmp_appkey = "76579579da14418ba97b1cbe38366410";
        //修改用户信息，把微信昵称加上去
        var userinfo_params = "userid=" + this.$store.state.userID + "&devicetype=7&loginpwd=123456&aliasname=" + encodeURI(this.$store.state.nickName);
        var userinfo_sign = getSign(userinfo_params + msmp_appkey);
        var userinfo_url = "/msmp/nscreen/modifynscreenuserinfo.action?userid=" + this.$store.state.userID + "&devicetype=7&loginpwd=123456&aliasname=" + encodeURI(this.$store.state.nickName) + "&sign=" + userinfo_sign;

        this.$http.get(userinfo_url, {headers: {Authorization: "Basic " + btoa("screen:screen123")}}).then(res => {
          var data = res.body;
        }, res => {
          alert("用户名出错！")
        })

        var that = this;
        wx.scanQRCode({
          needResult: 1, // 默认为0，扫描结果由微信处理，1则直接返回扫描结果，
          scanType: ["qrCode", "barCode"], // 可以指定扫二维码还是一维码，默认二者都有
          success: function (res) {
            //分割数据，可以用来判断扫的是条形码还是二维码 顺便init一些数据
//            that.test = res.resultStr;
            var data = res.resultStr.split(/_/);
            var randomCode = data[0];
            var STB_id = data[1];
            var userID = that.$store.state.userID;

            //条形码
            if (randomCode == "CODE") {
              var temp = STB_id.split(/,/);
              STB_id = temp[1];

              //查询验证码是否有效
              var url = '/cms/thirdPartyPortalInterface/validRandomCode.service?type=barcode&stbNo=' + STB_id + '&resFormat=json';
              that.$http.get(url).then(res => {
                var data = res.body;
                //失效
                if (data.validRandomCodeResponse.result == "false") {
                  alert("验证码已过期，请刷新机顶盒页面重新扫描绑定！");
                }
                //有效
                else if (data.validRandomCodeResponse.result == "true") {
                  //绑定的URL 参数 sign的设置
                  if (that.STB_name == "") {
                    that.STB_name = "客厅";
                  }
                  var bind_params = 'userid=' + userID + '&userpassword=123456&clientid=' + STB_id + '&devicetype=7&clientname=' + encodeURI(that.STB_name);
                  var bind_sign = getSign(bind_params + msmp_appkey);
                  var bind_url = '/msmp/nscreen/bind.action?userid=' + userID + '&userpassword=123456&clientid=' + STB_id + '&devicetype=7&clientname=' + encodeURI(that.STB_name) + '&sign=' + bind_sign;
                  //发送绑定请求
                  that.$http.get(bind_url, {headers: {Authorization: "Basic " + btoa("screen:screen123")}}).then(res => {
                    var bind_data = res.body;
                    //判断盒子能否绑定
                    var bind_code = bind_data.substring(bind_data.indexOf("<errorcode>") + 11, (bind_data.indexOf("</errorcode>")));
                    if (bind_code == "-10") {
                      alert("该机顶盒用户绑定已满！");
                    }
                    else if (bind_code == "-3") {
                      alert("您已绑定该机顶盒，无法重复绑定！");
                    }
                    else {
                      alert("绑定成功!");
                      this.toast = Toast({
                        message: '加载中...',
                        position: 'middle',
                        duration: -1
                      });
                      that.$store.commit("STBid", STB_id);

                      that.chaxun();
                    }
                  }, res => {
                    alert("绑定接口出错！请稍候再试!");
                  })
                }
              }, res => {
                alert("校验码接口错误！请稍候再试！");
              })
            }
            //二维码
            else {
              //先验证二维码的校验码是否有效
              var url = '/cms/thirdPartyPortalInterface/validRandomCode.service?type=dimension&stbNo=' + STB_id + '&randomCode=' + randomCode + '&resFormat=json';

              that.$http.get(url).then(res => {
                var data = res.body;
                //失效
                if (data.validRandomCodeResponse.result == "false") {
                  alert("验证码已过期，请刷新机顶盒页面重新扫描绑定！");
                }
                //有效
                else if (data.validRandomCodeResponse.result == "true") {
                  //有效则绑定机顶盒  (先拿userID)
                  //绑定的URL 参数 sign的设置
                  if (that.STB_name == "") {
                    that.STB_name = "客厅";
                  }
                  var bind_params = 'userid=' + userID + '&userpassword=123456&clientid=' + STB_id + '&devicetype=7&clientname=' + encodeURI(that.STB_name);
                  var bind_sign = getSign(bind_params + msmp_appkey);
                  var bind_url = '/msmp/nscreen/bind.action?userid=' + userID + '&userpassword=123456&clientid=' + STB_id + '&devicetype=7&clientname=' + encodeURI(that.STB_name) + '&sign=' + bind_sign;

                  //发送绑定请求
                  that.$http.get(bind_url, {headers: {Authorization: "Basic " + btoa("screen:screen123")}}).then(res => {
                    var bind_data = res.body;
                    //判断盒子能否绑定
                    var bind_code = bind_data.substring(bind_data.indexOf("<errorcode>") + 11, (bind_data.indexOf("</errorcode>")));
                    if (bind_code == "-10") {
                      alert("该机顶盒用户绑定已满！");
                    }
                    else if (bind_code == "-3") {
                      alert("您已绑定该机顶盒，无法重复绑定！");
                    }
                    else {
                      alert("绑定成功!");
                      this.toast = Toast({
                        message: '加载中...',
                        position: 'middle',
                        duration: -1
                      });
                      that.$store.commit("STBid", STB_id);

                      that.chaxun();
                    }
                  }, res => {
                    alert("绑定接口出错！请稍候再试!");
                  })
                }
              }, res => {
                alert("校验码接口错误！请稍候再试！");
              })
            }
          },
          error: function (res) {
            alert("扫描出错!");
          }
        });
      },
      dengluzhuce() {
        //1
        //有效则绑定机顶盒  (先拿userID)
        //直接拿openid注册，失败则是有账号了
        var that = this;
        var msmp_appkey = "76579579da14418ba97b1cbe38366410";

        var register_params = 'loginname=' + that.$store.state.openid + '&loginpwd=123456&devicetype=7&logintype=wechat&status=0&recommendno=&deviceno=' + that.$store.state.openid;

        var register_sign = getSign(register_params + msmp_appkey);

        var register_url = '/msmp/nscreen/register.action?loginname=' + that.$store.state.openid + '&loginpwd=123456&devicetype=7&logintype=wechat&status=0&recommendno=&deviceno=' + that.$store.state.openid + '&sign=' + register_sign;
        that.$http.get(register_url, {headers: {Authorization: "Basic " + btoa("screen:screen123")}}).then(res => {
          var register_data = res.body;
          var uid = register_data.substring(register_data.indexOf("<userid>") + 8, (register_data.indexOf("</userid>")));   //存储userid
          var code = register_data.substring(register_data.indexOf("<errorcode>") + 11, (register_data.indexOf("</errorcode>")));   //存储状态码

          if (code == "-3") {
            alert("超过最大绑定用户数!");
          }
          //登录1
          else if (code == "0") {

            //情况1：注册成功，讲userid存进vuex中，然后发送登录请求
            //存userID
            that.$store.commit("userID", uid == "" ? 0 : uid);

            //设置login的URL 参数 sign的设置
            var login_params = "loginname=" + that.$store.state.openid + "&loginpwd=123456&devicetype=7&logintype=wechat&resultformat=JSON";
            var login_sign = getSign(login_params + msmp_appkey);
            var login_url = "/msmp/nscreen/login.action?loginname=" + that.$store.state.openid + "&loginpwd=123456&devicetype=7&logintype=wechat&resultformat=JSON&sign=" + login_sign;
            //发送登录请求
            that.$http.get(login_url, {headers: {Authorization: "Basic " + btoa("screen:screen123")}}).then(res => {
              var login_data = res.body;
              //将userid存入vuex
              that.STB_List = login_data.clients;
              if (that.STB_List.length == 0) {
                that.$store.commit("isHaveSTB", false);
              } else {
                that.$store.commit("isHaveSTB", true);
              }
              that.toast.close();
              that.$refs.loadmore.onTopLoaded();
            }, res => {
              alert("登录1接口错误！");
            })
          }
          //登录2
          else if (code == "-1") {
            //login的URL 参数 sign的设置
            var login_params = "loginname=" + that.$store.state.openid + "&loginpwd=123456&devicetype=7&logintype=wechat&resultformat=JSON";
            var login_sign = getSign(login_params + msmp_appkey);
            var login_url = "/msmp/nscreen/login.action?loginname=" + that.$store.state.openid + "&loginpwd=123456&devicetype=7&logintype=wechat&resultformat=JSON&sign=" + login_sign;
            //发送登录请求
            that.$http.get(login_url, {headers: {Authorization: "Basic " + btoa("screen:screen123")}}).then(res => {
              var login_data = res.body;
              //将userid存入vuex
              that.$store.commit("userID", login_data.userid == "" ? 0 : login_data.userid);

              that.chaxun();
            }, res => {
              alert("登录2接口错误！");
            })
          }
        }, res => {
          alert("注册接口出错！请稍候再试!");
        })
      },
      upDatePage() {
        //login的URL 参数 sign的设置
        var that = this;
        var msmp_appkey = "76579579da14418ba97b1cbe38366410";
        var login_params = "loginname=" + that.$store.state.openid + "&loginpwd=123456&devicetype=7&logintype=wechat&resultformat=JSON";
        var login_sign = getSign(login_params + msmp_appkey);
        var login_url = "/msmp/nscreen/login.action?loginname=" + that.$store.state.openid + "&loginpwd=123456&devicetype=7&logintype=wechat&resultformat=JSON&sign=" + login_sign;
        //发送登录请求
        that.$http.get(login_url, {headers: {Authorization: "Basic " + btoa("screen:screen123")}}).then(res => {
          var login_data = res.body;
          //将userid存入vuex
          if (login_data.errorcode == -2) {
            that.chaxun();
          } else {
            that.STB_List = login_data.clients;
            if (that.STB_List.length == 0) {
              that.$store.commit("isHaveSTB", false);
            } else {
              that.$store.commit("isHaveSTB", true);
            }
          }
          that.$store.commit("userID", login_data.userid == "" ? 0 : login_data.userid);
          that.toast.close();
          that.$refs.loadmore.onTopLoaded();
        }, res => {
          alert("登录2接口错误！");
        })
      },
      wxUpDatePage() {
        //login的URL 参数 sign的设置
        var that = this;
        var msmp_appkey = "76579579da14418ba97b1cbe38366410";
        var login_params = "loginname=" + that.$store.state.openid + "&loginpwd=123456&devicetype=7&logintype=wechat&resultformat=JSON";
        var login_sign = getSign(login_params + msmp_appkey);
        var login_url = "/msmp/nscreen/login.action?loginname=" + that.$store.state.openid + "&loginpwd=123456&devicetype=7&logintype=wechat&resultformat=JSON&sign=" + login_sign;
        //发送登录请求
        that.$http.get(login_url, {headers: {Authorization: "Basic " + btoa("screen:screen123")}}).then(res => {
          var login_data = res.body;
          //将userid存入vuex
          if (login_data.errorcode == -2) {
            that.chaxun();
          } else {
            that.STB_List = login_data.clients;
            if (that.STB_List.length == 0) {
              that.$store.commit("isHaveSTB", false);
            } else {
              that.STB_List = login_data.clients;
              if (that.STB_List.length == 0) {
                that.$store.commit("isHaveSTB", false);
              } else {
                that.$store.commit("isHaveSTB", true);
                for (var i = 0; i < that.STB_List.length; i++) {
                  if (that.STB_List[i].clientid == that.$store.state.STBid) {
                    that.$store.commit("STBactiveIndex", i);
                    that.$store.commit("STBname", that.STB_List[i].clientname);
                  }
                }
              }
            }
          }
          that.$store.commit("userID", login_data.userid == "" ? 0 : login_data.userid);
          that.toast.close();
          that.$refs.loadmore.onTopLoaded();
        }, res => {
          alert("登录2接口错误！");
        })
      },
      chaxun() {
        var chaxun_url = "/cms/externalmsmp/getnscreenbindclient.service?userid=" + this.$store.state.userID + "&devicetype=7";

        this.$http.get(chaxun_url).then(res => {
          var chaxun_data = res.body;
          console.log(chaxun_data.clientlist);
          this.STB_List = chaxun_data.clientlist;

          if (this.STB_List == undefined || this.STB_List.length == 0) {
            this.$store.commit("isHaveSTB", false);
          } else {
            this.$store.commit("isHaveSTB", true);
            for (var i = 0; i < this.STB_List.length; i++) {
              if (this.STB_List[i].clientid == this.$store.state.STBid) {
                this.$store.commit("STBactiveIndex", i);
                this.$store.commit("STBname", this.STB_List[i].clientname);
              }
            }
          }
          if (this.toast !== null) {
            this.toast.close();
          }
          this.$refs.loadmore.onTopLoaded();
        }, res => {
          alert("查询接口出错!");
        })
      },

      changeTipsShow() {
        this.isTipsShow = false;
      },
      changeSTB_index(index) {
        this.stb_index = index;
        this.stb_name = this.STB_List[index].clientname;
        this.isSTBmsgShow = true;
      },
      closeSTBshow() {
        this.isSTBmsgShow = false;
      },
      changeSTBmsg() {
        var msmp_appkey = "76579579da14418ba97b1cbe38366410";
        var userID = this.$store.state.userID;

        if (this.STB_List.length == 0 || this.STB_List == undefined) {
          return
        }

        var STB_id = this.STB_List[this.stb_index].clientid;

        var bind_params = 'userid=' + userID + '&clientid=' + STB_id + '&devicetype=7&clientname=' + encodeURI(this.stb_name);
        var bind_sign = getSign(bind_params + msmp_appkey);
        var bind_url = '/msmp/nscreen/setclientname.action?userid=' + userID + '&clientid=' + STB_id + '&devicetype=7&clientname=' + encodeURI(this.stb_name) + '&sign=' + bind_sign;
        //发送绑定请求
        this.$http.get(bind_url, {headers: {Authorization: "Basic " + btoa("screen:screen123")}}).then(res => {
          var bind_data = res.body;
          //判断盒子能否绑定
          var bind_code = bind_data.substring(bind_data.indexOf("<errorcode>") + 11, (bind_data.indexOf("</errorcode>")));

          if (bind_code == 0) {
            this.chaxun();
            this.closeSTBshow();
            Toast("修改成功！");
          }
          else {
            this.closeSTBshow();
            Toast("修改失败！请稍后再试!");
          }

        }, res => {
          alert("绑定接口出错！请稍候再试!");
        })
      },

      aaa() {

      },
      yuyinan() {
        wx.startRecord();
        this.is = true;
      },
      yuyinsong() {
        var that = this;
        wx.stopRecord({
          success: function (res) {
            var localId = res.localId;
            wx.translateVoice({
              localId: localId, // 需要识别的音频的本地Id，由录音相关接口获得
              isShowProgressTips: 1, // 默认为1，显示进度提示
              success: function (res) {
                that.is = false;
                that.yuyinres = res.translateResult; // 语音识别的结果
              }
            });
          }
        });
      }
    },
    components: {},
    computed: {
      changeClientid() {
        if (this.STB_List == undefined) {
          return "暂无数据";
        } else {
          return this.STB_List.length == 0 ? "暂无数据" : this.STB_List[this.stb_index].clientid;
        }
      },
      mounted() {
      },
      changeTVno() {
        if (this.STB_List == undefined) {
          return "暂无数据";
        } else {
          return this.STB_List.length == 0 ? "暂无数据" : this.STB_List[this.stb_index].tvno;
        }
      }
    },
    filters: {
      STBactive(v1, v2, v3){
        return v2 == v3 ? v1 + " border2" : v1 + " border1";
      },
      STBisConnect(v1, v2, v3){
        return v2 == v3 ? "已连接" : v1;
      }
    }
  }
</script>

<style scoped>

  #yuyin {
    width: 50%;
    height: 40px;
    line-height: 40px;
    background-color: #fff;
    text-align: center;
    margin: 0 auto;
  }

  .yuyintu {
    width: 50%;
    height: 50px;
    background-color: #fff;
    text-align: center;
    line-height: 50px;
    position: fixed;
    bottom: 100px;
  }

  p {
    margin: 0;
    padding: 0;
  }

  #STB {
    width: 100%;
    height: 100vh;
    background-color: #ededed;
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

  header .add {
    font-size: 2.5vh;
    color: #FF3E05;
    position: absolute;
    right: 3vh;
    /*top: 2.25vh;*/
  }

  #tips {
    width: 100%;
    height: 5vh;
    background-color: #FEFCED;
    font-size: 12px;
    line-height: 5.5vh;
  }
  #tips .mar {
    float: left;
    width: 83%;
    height: 5vh;
    margin-left: 2%;
  }
  #tips .voice {
    float: left;
    width: 5vw;
    margin: 2.5vh 0 0 2vw;
    transform: translateY(-2.5vw);
  }
  #tips .close {
    float: right;
    width: 3vw;
    margin: 2.5vh 2vw 0 0;
    transform: translateY(-1.5vw);
  }

  #banner {
    width: 100%;
    height: 27vh;
    background-color: #30303c;
  }
  #banner img {
    width: 15%;
    margin: 0 auto;
    transform: translateY(9vh);
  }

  #STB_connect {
    width: 100%;
    height: 100vh;
    position: fixed;
    top: 0;
    z-index: 999999999;
    background-color: rgba(0, 0, 0, .8);
  }
  #STB_connect .con {
    width: 86vw;
    height: 65vw;
    border: 1px solid #c3c3c3;
    background-color: #fff;
    position: absolute;
    top: 30vh;
    left: 7vw;
    z-index: 999;
  }
  #STB_connect .con input {
    display: block;
    width: 90%;
    height: 30px;
    font-size: 16px;
    margin: 0 auto;
    transform: translateY(3vh);
    border-left: 0;
    border-right: 0;
    border-top: 0;
  }
  #STB_connect .con .tips {
    width: 90%;
    margin: 0 auto;
    transform: translateY(30px);
    color: #b9b9b9;
  }
  #STB_connect .con .yes {
    display: block;
    width: 20vw;
    height: 7vw;
    line-height: 7vw;
    text-align: center;
    /*color: #fff;*/
    /*background-color: #00cd48;*/
    border: 1px solid #bcbcbc;
    position: absolute;
    bottom: 20px;
    left: 15vw;
  }
  #STB_connect .con .no {
    display: block;
    width: 20vw;
    height: 7vw;
    line-height: 7vw;
    text-align: center;
    /*color: #fff;*/
    /*background-color: #ff3e05;*/
    border: 1px solid #bcbcbc;
    position: absolute;
    bottom: 20px;
    right: 15vw;
  }

  #STB_msg {
    width: 100%;
    height: 100vh;
    position: fixed;
    top: 0;
    z-index: 999999999;
    background-color: rgba(0, 0, 0, .8);
  }
  #STB_msg .con {
    width: 86vw;
    height: 65vw;
    border: 1px solid #c3c3c3;
    background-color: #fff;
    position: absolute;
    top: 30vh;
    left: 7vw;
    z-index: 999;
  }
  #STB_msg .con .title {
    height: 30px;
    line-height: 30px;
    font-size: 14px;
    text-align: center;
    transform: translateY(10px);
  }
  #STB_msg .con .item {
    width: 80%;
    height: 30px;
    line-height: 30px;
    font-size: 12px;
    margin: 10px auto 0 auto;
    transform: translateY(10px);
  }
  #STB_msg .con .item:nth-child(2), #STB_msg .con .item:nth-child(3) {
    color: #c3c3c3;
  }
  #STB_msg .con .item:nth-child(4) {

  }
  #STB_msg .con .yes {
    display: block;
    width: 20vw;
    height: 7vw;
    line-height: 7vw;
    text-align: center;
    font-size: 12px;
    border: 1px solid #bcbcbc;
    position: absolute;
    bottom: 20px;
    left: 15vw;
  }
  #STB_msg .con .no {
    display: block;
    width: 20vw;
    height: 7vw;
    line-height: 7vw;
    text-align: center;
    font-size: 12px;
    border: 1px solid #bcbcbc;
    position: absolute;
    bottom: 20px;
    right: 15vw;
  }

  .STBs {
    width: 100%;
    height: 8vh;
    background-color: #fff;
    margin-bottom: 8px;
  }
  .STBs img {
    width: 4vh;
    margin: 2vh 0 0 5vw;
    float: left;
  }
  .STBs .STB_name {
    height: 8vh;
    line-height: 8vh;
    font-size: 18px;
    float: left;
    margin-left: 5vw;
  }
  .STBs .Connection_state {
    display: block;
    width: 14vw;
    height: 4vh;
    font-size: 14px;
    line-height: 4vh;
    text-align: center;
    float: right;
    margin: 2vh 5vw 0 0;
  }
  .border1 {
    border: 2px solid #c7c7c7;
    border-radius: 4vh;
    color: #c7c7c7;
  }
  .border2 {
    border: 2px solid #04B456;
    border-radius: 4vh;
    color: #04B456;
  }

  .mint-loadmore {
    width: 100%;
    height: 50vh;
    overflow: hidden;
  }
  .mint-loadmore-top span {
    display: inline-block;
    vertical-align: middle;
    transition: all .25s;
  }
  .rotate {
    transform: rotate(180deg);
  }

  .notConnect {
    width: 60vw;
    margin: 0 auto;
    margin-top: 15vh;
  }
</style>
