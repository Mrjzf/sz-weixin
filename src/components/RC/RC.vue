<template>
  <div id="rc">

    <header>
      <span id="STBname" v-if="$store.state.STBid==''">未绑定机顶盒</span>
      <span id="STBname" v-else>{{ $store.state.STBname }}</span>
      <span id="switch" @click="goSTBlist();">切换</span>
    </header>

    <div id="allBtn">

      <div :class="'ending1' | fclsEnding('isActiveA1', judge)" @touchstart.prevent="startActive('ending')"
           @touchmove.prevent="aaa" @touchend="endActive('KEY_POWER')">
        <div :class="'ending2 icon-chs_sywx_ic_detail_stop' | fclsEnding('isActiveA2', judge)"></div>
      </div>

      <div class="volume1">
        <div class="volume2">
          <div :class="'volSub icon-chs_sywx_ic_detail_jian' | fclsVolSub('isActiveC', judge)"
               @touchstart.prevent="startActive('volsub')" @touchmove.prevent="aaa" @touchend="endActive('KEY_VOLUME_DOWN')"></div>
          音量
          <div :class="'volAdd icon-chs_sywx_ic_detail_jia' | fclsVolAdd('isActiveC', judge)"
               @touchstart.prevent="startActive('voladd')" @touchmove.prevent="aaa" @touchend="endActive('KEY_VOLUME_UP')"></div>
        </div>
      </div>

      <div :class="'menu1' | fclsMenu('isActiveA1', judge)" @touchstart.prevent="startActive('menu')" @touchmove.prevent="aaa" @touchend="endActive('KEY_MENU')">
        <div :class="'menu2' | fclsMenu('isActiveA2', judge)">菜单</div>
      </div>

      <div class="disk1">
        <div class="disk2">

          <!--上下箭头-->
          <div :class="'up icon-chs_sywx_ic_detail_jiant' | fclsUp('isActiveC', judge)" @touchstart.prevent="startActive('up')"
               @touchmove.prevent="aaa" @touchend="endActive('KEY_UP')"></div>
          <div :class="'down icon-chs_sywx_ic_detail_jiant' | fclsDown('isActiveC', judge)"
               @touchstart.prevent="startActive('down')" @touchmove.prevent="aaa" @touchend="endActive('KEY_DOWN')"></div>

          <!--确认按钮-->
          <div class="s_disk1" @touchstart.prevent="startActive('s_disk')" @touchmove.prevent="aaa" @touchend="endActive('KEY_OK')">
            <div :class="'s_disk2' | fclsS_disk('isActiveA2', judge)">
              确认
            </div>
          </div>

          <!--左右箭头-->
          <div :class="'left icon-chs_sywx_ic_detail_jiant' | fclsLeft('isActiveC', judge)"
               @touchstart.prevent="startActive('left')" @touchmove.prevent="aaa" @touchend="endActive('KEY_LEFT')"></div>
          <div :class="'right icon-chs_sywx_ic_detail_jiant' | fclsRight('isActiveC', judge)"
               @touchstart.prevent="startActive('right')" @touchmove.prevent="aaa" @touchend="endActive('KEY_RIGHT')"></div>

        </div>
      </div>

      <div :class="'exit1' | fclsExit('isActiveA1', judge)" @touchstart.prevent="startActive('exit')" @touchmove.prevent="aaa" @touchend="endActive('KEY_QUIT')">
        <div :class="'exit2' | fclsExit('isActiveA2', judge)">
          退出
        </div>
      </div>

      <div class="channel1" @click="inputShow">
        <div class="channel2">
          输入文字
        </div>
      </div>

      <div :class="'back1' | fclsBack('isActiveA1', judge)" @touchstart.prevent="startActive('back')" @touchmove.prevent="aaa" @touchend="endActive('KEY_BACK')">
        <div :class="'back2' | fclsBack('isActiveA2', judge)">
          返回
        </div>
      </div>

      <!--不用管的线-->
      <span class="cut_line"></span>
      <!--<span class="area_line1"></span>-->
      <!--<span class="area_line2"></span>-->

      <!--<div :class="'icon-chs_sywx_ic_detail_livez' | fclsLivez('isActiveB', judge)" @touchstart="startActive('livez')" @touchend="endActive()">-->
      <!--<div class="icon1">直播助手</div>-->
      <!--</div>-->

      <!--<div :class="'icon-chs_sywx_ic_detail_vodz' | fclsVodz('isActiveB', judge)" @touchstart="startActive('vodz')" @touchend="endActive()">-->
      <!--<div class="icon2">点播助手</div>-->
      <!--</div>-->

      <div :class="'icon-chs_sywx_ic_detail_num' | fclsNum('isActiveB', judge)" @touchstart="startActive('num')" @touchend="endActive()" @click="numberShow();">
        <div class="icon3">数字键</div>
      </div>

      <!--<div :class="'icon-chs_sywx_ic_detail_setting' | fclsSetting('isActiveB', judge)" @touchstart="startActive('setting')" @touchend="endActive()">-->
      <!--<div class="icon4">设置</div>-->
      <!--</div>-->

    </div>

    <transition name="slide-fade">
      <div id="number" v-show="isnumberShow" @click="numberShow();"></div>
    </transition>
    <transition name="slide-fade">
      <div id="number_con" v-show="isnumberShow">
        <div id="F_keys">
          <div id="page">
            <div class="pagePre">
              <img src="../../assets/images/RC/key_icon_syy.png" alt="" v-show="!isPreclick" @touchstart.prevent="Preclick"
                   @touchmove.prevent="aaa" @touchend="Preclick">
              <img src="../../assets/images/RC/key_icon_syy_s.png" alt="" v-show="isPreclick">
            </div>
            <div class="pageNext">
              <img src="../../assets/images/RC/key_icon_xyy.png" alt="" v-show="!isNextclick" @touchstart.prevent="Nextclick"
                   @touchmove.prevent="aaa" @touchend="Nextclick">
              <img src="../../assets/images/RC/key_icon_xyy_s.png" alt="" v-show="isNextclick">
            </div>
          </div>
          <div class="F">
            <img src="../../assets/images/RC/key_icon_f1_n.png" alt="" v-show="!isF1click" @touchstart.prevent="F1click"
                 @touchmove.prevent="aaa" @touchend="F1click">
            <img src="../../assets/images/RC/key_icon_f1_s.png" alt="" v-show="isF1click">
          </div>
          <div class="F">
            <img src="../../assets/images/RC/key_icon_f2_n.png" alt="" v-show="!isF2click" @touchstart.prevent="F2click"
                 @touchmove.prevent="aaa" @touchend="F2click">
            <img src="../../assets/images/RC/key_icon_f2_s.png" alt="" v-show="isF2click">
          </div>
          <div class="F">
            <img src="../../assets/images/RC/key_icon_f3_n.png" alt="" v-show="!isF3click" @touchstart.prevent="F3click"
                 @touchmove.prevent="aaa" @touchend="F3click">
            <img src="../../assets/images/RC/key_icon_f3_s.png" alt="" v-show="isF3click">
          </div>
          <div class="F">
            <img src="../../assets/images/RC/key_icon_f4_n.png" alt="" v-show="!isF4click" @touchstart.prevent="F4click"
                 @touchmove.prevent="aaa" @touchend="F4click">
            <img src="../../assets/images/RC/key_icon_f4_s.png" alt="" v-show="isF4click">
          </div>
        </div>
        <div id="num_keys">
          <div class="num">
            <img src="../../assets/images/RC/key_icon_1_n.png" alt="" v-show="!is1click" @touchstart.prevent="click1"
                 @touchmove.prevent="aaa" @touchend="click1">
            <img src="../../assets/images/RC/key_icon_1_s.png" alt="" v-show="is1click">
          </div>
          <div class="num">
            <img src="../../assets/images/RC/key_icon_2_n.png" alt="" v-show="!is2click" @touchstart.prevent="click2"
                 @touchmove.prevent="aaa" @touchend="click2">
            <img src="../../assets/images/RC/key_icon_2_s.png" alt="" v-show="is2click">
          </div>
          <div class="num">
            <img src="../../assets/images/RC/key_icon_3_n.png" alt="" v-show="!is3click" @touchstart.prevent="click3"
                 @touchmove.prevent="aaa" @touchend="click3">
            <img src="../../assets/images/RC/key_icon_3_s.png" alt="" v-show="is3click">
          </div>

          <div class="num">
            <img src="../../assets/images/RC/key_icon_4_n.png" alt="" v-show="!is4click" @touchstart.prevent="click4"
                 @touchmove.prevent="aaa" @touchend="click4">
            <img src="../../assets/images/RC/key_icon_4_s.png" alt="" v-show="is4click">
          </div>
          <div class="num">
            <img src="../../assets/images/RC/key_icon_5_n.png" alt="" v-show="!is5click" @touchstart.prevent="click5"
                 @touchmove.prevent="aaa" @touchend="click5">
            <img src="../../assets/images/RC/key_icon_5_s.png" alt="" v-show="is5click">
          </div>
          <div class="num">
            <img src="../../assets/images/RC/key_icon_6_n.png" alt="" v-show="!is6click" @touchstart.prevent="click6"
                 @touchmove.prevent="aaa" @touchend="click6">
            <img src="../../assets/images/RC/key_icon_6_s.png" alt="" v-show="is6click">
          </div>

          <div class="num">
            <img src="../../assets/images/RC/key_icon_7_n.png" alt="" v-show="!is7click" @touchstart.prevent="click7"
                 @touchmove.prevent="aaa" @touchend="click7">
            <img src="../../assets/images/RC/key_icon_7_s.png" alt="" v-show="is7click">
          </div>
          <div class="num">
            <img src="../../assets/images/RC/key_icon_8_n.png" alt="" v-show="!is8click" @touchstart.prevent="click8"
                 @touchmove.prevent="aaa" @touchend="click8">
            <img src="../../assets/images/RC/key_icon_8_s.png" alt="" v-show="is8click">
          </div>
          <div class="num">
            <img src="../../assets/images/RC/key_icon_9_n.png" alt="" v-show="!is9click" @touchstart.prevent="click9"
                 @touchmove.prevent="aaa" @touchend="click9">
            <img src="../../assets/images/RC/key_icon_9_s.png" alt="" v-show="is9click">
          </div>

          <div class="num">
            <img src="../../assets/images/RC/key_icon_vod_n.png" alt="" v-show="!isDBclick" @touchstart.prevent="clickDB"
                 @touchmove.prevent="aaa" @touchend="clickDB">
            <img src="../../assets/images/RC/key_icon_vod_s.png" alt="" v-show="isDBclick">
          </div>
          <div class="num">
            <img src="../../assets/images/RC/key_icon_0_n.png" alt="" v-show="!is0click" @touchstart.prevent="click0"
                 @touchmove.prevent="aaa" @touchend="click0">
            <img src="../../assets/images/RC/key_icon_0_s.png" alt="" v-show="is0click">
          </div>
          <div class="num">
            <img src="../../assets/images/RC/key_icon_tv_n.png" alt="" v-show="!isDSclick" @touchstart.prevent="clickDS"
                 @touchmove.prevent="aaa" @touchend="clickDS">
            <img src="../../assets/images/RC/key_icon_tv_s.png" alt="" v-show="isDSclick">
          </div>
        </div>
      </div>
    </transition>

    <transition name="fade">
      <div id="input" v-show="isinputShow">
        <div id="input_con">
          <input type="text" placeholder="请输入内容" v-model="sendMsg1">
          <span class="lineX"></span>
          <span class="lineY"></span>
          <p id="yes" @click="message">确定</p>
          <p id="no" @click="inputShow">取消</p>
        </div>
      </div>
    </transition>

  </div>
</template>

<script>

  import '@/assets/js/strophe'
  import '@/assets/js/strophe.muc'
  import '@/assets/js/strophe.register'
  import {Toast} from 'mint-ui';

  export default {
    data(){
      return {
        judge: "",
        classChange1: "",
        classChange2: "",
        toast: null,
        server: 'dp0.sz96296.com',
        BOSH_SERVICE: 'ws://122.193.8.100:7076/websocket',
        sendMsg1: "",
        isnumberShow: false,
        isinputShow: false,
        isF1click: false,
        isF2click: false,
        isF3click: false,
        isF4click: false,
        is1click: false,
        is2click: false,
        is3click: false,
        is4click: false,
        is5click: false,
        is6click: false,
        is7click: false,
        is8click: false,
        is9click: false,
        is0click: false,
        isDBclick: false,
        isDSclick: false,
        isPreclick: false,
        isNextclick: false
      }
    },
    created() {
//      this.keyMap();
      this.$store.commit("isSmallRCshow", false);
//      this.xmpp();
    },
    mounted() {
      this.$store.commit("isAppShow", true);
    },
    destroyed(){
      this.$store.commit("isAppShow", false);
      this.$store.commit("isSmallRCshow", true);
    },
    updated(){
      this.$store.commit("isSmallRCshow", false);
    },
    methods: {
      aaa() {

      },
      Preclick(e) {
        this.isPreclick = !this.isPreclick;
        if (e.type == "touchend") {
          this.$emit("send", "KEY_PAGEUP");
        }
      },
      Nextclick(e) {
        this.isNextclick = !this.isNextclick;
        if (e.type == "touchend") {
          this.$emit("send", "KEY_PAGEDOWN");
        }
      },
      F1click(e) {
        this.isF1click = !this.isF1click;
        if (e.type == "touchend") {
          this.$emit("send", "KEY_RED");
        }
      },
      F2click(e) {
        this.isF2click = !this.isF2click;
        if (e.type == "touchend") {
          this.$emit("send", "KEY_BLUE");
        }
      },
      F3click(e) {
        this.isF3click = !this.isF3click;
        if (e.type == "touchend") {
          this.$emit("send", "KEY_GREEN");
        }
      },
      F4click(e) {
        this.isF4click = !this.isF4click;
        if (e.type == "touchend") {
          this.$emit("send", "KEY_YELLOW");
        }
      },
      click1(e) {
        this.is1click = !this.is1click;
        if (e.type == "touchend") {
          this.$emit("send", "1");
        }
      },
      click2(e) {
        this.is2click = !this.is2click;
        if (e.type == "touchend") {
          this.$emit("send", "2");
        }
      },
      click3(e) {
        this.is3click = !this.is3click;
        if (e.type == "touchend") {
          this.$emit("send", "3");
        }
      },
      click4(e) {
        this.is4click = !this.is4click;
        if (e.type == "touchend") {
          this.$emit("send", "4");
        }
      },
      click5(e) {
        this.is5click = !this.is5click;
        if (e.type == "touchend") {
          this.$emit("send", "5");
        }
      },
      click6(e) {
        this.is6click = !this.is6click;
        if (e.type == "touchend") {
          this.$emit("send", "6");
        }
      },
      click7(e) {
        this.is7click = !this.is7click;
        if (e.type == "touchend") {
          this.$emit("send", "7");
        }
      },
      click8(e) {
        this.is8click = !this.is8click;
        if (e.type == "touchend") {
          this.$emit("send", "8");
        }
      },
      click9(e) {
        this.is9click = !this.is9click;
        if (e.type == "touchend") {
          this.$emit("send", "9");
        }
      },
      click0(e) {
        this.is0click = !this.is0click;
        if (e.type == "touchend") {
          this.$emit("send", "0");
        }
      },
      clickDB(e) {
        this.isDBclick = !this.isDBclick;
        if (e.type == "touchend") {
          this.$emit("send", "KEY_VOD");
        }
      },
      clickDS(e) {
        this.isDSclick = !this.isDSclick;
        if (e.type == "touchend") {
          this.$emit("send", "KEY_TV");
        }
      },
      message() {
        if (this.sendMsg1 == "") {
          return
        } else {
          this.$emit('send', 'message', this.sendMsg1);
          this.sendMsg1 = "";
        }
      },
      startActive(btn){
        this.judge = btn;
      },
      goSTBlist() {
        if (this.$store.state.openid == 0 || this.$store.state.openid == undefined || this.$store.state.openid == -1) {
          this.$emit("send", "8");
        } else {
          this.$router.push({path: 'STB_list'});
        }
      },
      endActive(key){
        this.judge = "";
        if (key == undefined) {
          return
        }
        this.$emit("send", key);
      },
      keyMap() {
        var url = '/cms/thirdPartyPortalInterface/getNSRegionKeyMap.service?resFormat=json&nsRegionId=' + this.$store.state.areaID;

        this.$http.get(url).then(res => {
          var data = res.body;
//          console.log(data);
        }, res => {
          console.log("遥控错误");
        })
      },
      xmpp() {
        if (this.$store.state.openid == 0 || this.$store.state.openid == undefined || this.$store.state.openid == -1) {
          alert("请先登录！");
          return
        }
        if (this.$store.state.connection == null) {
          this.toast = Toast({
            message: "连接中,给我几秒,请稍等...",
            duration: 10000
          });
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

        if (this.$store.state.STBid == "") {
          alert("请先绑定机顶盒!");
          this.$router.push({path: "STB_list"});
          return
        }

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
        else if (msg == "KEY_RED") {
          con = '<Keyboard><InstanceID>0</InstanceID><Unit>KEYBOARD_CODE</Unit><Target>-16777179</Target></Keyboard>';
        }
        else if (msg == "KEY_BLUE") {
          con = '<Keyboard><InstanceID>0</InstanceID><Unit>KEYBOARD_CODE</Unit><Target>-16777178</Target></Keyboard>';
        }
        else if (msg == "KEY_GREEN") {
          con = '<Keyboard><InstanceID>0</InstanceID><Unit>KEYBOARD_CODE</Unit><Target>-16777177</Target></Keyboard>';
        }
        else if (msg == "KEY_YELLOW") {
          con = '<Keyboard><InstanceID>0</InstanceID><Unit>KEYBOARD_CODE</Unit><Target>-16777176</Target></Keyboard>';
        }
        else if (msg == "SEND_MSG") {
          con = '<InputMethod><InstanceID>0</InstanceID><Unit>InputText</Unit><Target>' + this.sendMsg + '</Target></InputMethod>';
        }

        var m = $msg({
          to: to, //发送过去的地址(机顶盒号id+域名)
          from: from, //本地地址
          type: 'chat'
        }).c("body").t(con);

        this.$store.state.connection.send(m);

      },
      numberShow() {
        this.$store.commit("isAppShow", this.$store.state.isAppShow == true ? false : true);
        this.isnumberShow = !this.isnumberShow;
      },
      inputShow() {
        this.isinputShow = !this.isinputShow;
        this.sendMsg1 = "";
      }
    },
    filters: {
      fclsEnding(v1, v2, v3){
        var v = {};
        v[v1] = true;
        v[v2] = (v3 == "ending");
        return v;
      },
      fclsMenu(v1, v2, v3){
        var v = {};
        v[v1] = true;
        v[v2] = (v3 == "menu");
        return v;
      },
      fclsExit(v1, v2, v3){
        var v = {};
        v[v1] = true;
        v[v2] = (v3 == "exit");
        return v;
      },
      fclsBack(v1, v2, v3){
        var v = {};
        v[v1] = true;
        v[v2] = (v3 == "back");
        return v;
      },
      fclsS_disk(v1, v2, v3){
        var v = {};
        v[v1] = true;
        v[v2] = (v3 == "s_disk");
        return v;
      },
      fclsLivez(v1, v2, v3){
        var v = {};
        v[v1] = true;
        v[v2] = (v3 == "livez");
        return v;
      },
      fclsVodz(v1, v2, v3){
        var v = {};
        v[v1] = true;
        v[v2] = (v3 == "vodz");
        return v;
      },
      fclsNum(v1, v2, v3){
        var v = {};
        v[v1] = true;
        v[v2] = (v3 == "num");
        return v;
      },
      fclsSetting(v1, v2, v3){
        var v = {};
        v[v1] = true;
        v[v2] = (v3 == "setting");
        return v;
      },
      fclsUp(v1, v2, v3){
        var v = {};
        v[v1] = true;
        v[v2] = (v3 == "up");
        return v;
      },
      fclsDown(v1, v2, v3){
        var v = {};
        v[v1] = true;
        v[v2] = (v3 == "down");
        return v;
      },
      fclsLeft(v1, v2, v3){
        var v = {};
        v[v1] = true;
        v[v2] = (v3 == "left");
        return v;
      },
      fclsRight(v1, v2, v3){
        var v = {};
        v[v1] = true;
        v[v2] = (v3 == "right");
        return v;
      },
      fclsVolAdd(v1, v2, v3){
        var v = {};
        v[v1] = true;
        v[v2] = (v3 == "voladd");
        return v;
      },
      fclsVolSub(v1, v2, v3){
        var v = {};
        v[v1] = true;
        v[v2] = (v3 == "volsub");
        return v;
      },
    }
  }
</script>

<style scoped>

  p {
    margin: 0;
    padding: 0;
  }

  #rc {
    width: 100%;
    height: 100vh;
    overflow: hidden;
    background-color: #EAEAEA;
    position: relative;
  }

  header {
    width: 100%;
    height: 7vh;
    background-color: #fff;
    line-height: 7vh;
    font-size: 18px;
    position: fixed;
    z-index: 1;
  }

  #STBname {
    display: block;
    text-align: center;
  }

  #switch {
    position: absolute;
    top: 0;
    right: 6vw;
    font-size: 14px;
    color: #FF3E05;
  }

  .slide-fade-enter-active, .slide-fade-leave-active {
    transition: all .5s ease;
  }

  .slide-fade-enter, .slide-fade-leave-active {
    transform: translateY(50vh);
    opacity: 0;
  }

  #number {
    position: fixed;
    z-index: 10;
    width: 100%;
    height: 100vh;
    background: rgba(0, 0, 0, .7);
  }

  #number_con {
    width: 100%;
    height: 80vh;
    position: fixed;
    z-index: 999;
    bottom: 0;
    background-color: #fff;
  }

  .fade-enter-active, .fade-leave-active {
    transition: opacity .3s;
  }
  .fade-enter, .fade-leave-to {
    opacity: 0
  }

  #input {
    width: 100%;
    height: 100vh;
    position: fixed;
    z-index: 20;
    background: rgba(0, 0, 0, 0.9);
  }
  #input_con {
    width: 70%;
    height: 150px;
    background-color: #fff;
    position: fixed;
    top: 30%;
    left: 50%;
    transform: translateX(-50%);
  }
  #input_con input {
    display: block;
    width: 80%;
    height: 20px;
    margin: 0 auto;
    transform: translateY(20px);
  }
  #input_con .lineX {
    display: block;
    width: 100%;
    height: 1px;
    background-color: #c3c3c3;
    position: absolute;
    bottom: 13vw;
  }
  #input_con .lineY {
    display: block;
    width: 13vw;
    height: 1px;
    background-color: #c3c3c3;
    position: absolute;
    bottom: 6.5vw;
    left: 28.5vw;
    transform: rotate(90deg);
  }
  #input_con p {
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

  #F_keys {
    width: 100%;
    height: 30vw;
    border-bottom: 1px solid #cbcbcb;
  }

  #page {
    width: 100%;
    overflow: hidden;
    margin-top: 3vw;
  }
  #page .pagePre {
    width: 20vw;
    float: left;
    margin-left: 5vw;
  }
  #page .pageNext {
    width: 20vw;
    float: right;
    margin-right: 5vw;
  }

  #F_keys .F {
    width: 17vw;
    float: left;
    margin: 2vw 3.5vw 0 3.5vw;
  }

  #F_keys .F div {
    overflow: hidden;
  }

  #F_keys .F:nth-child(2) {
    margin-left: 5.5vw;
  }

  #num_keys {
    width: 100%;
    margin-top: 1vh;
  }

  #num_keys .num {
    width: 20vw;
    float: left;
    margin: 0vh 5vw 1vw 5vw;
  }

  #num_keys .num:nth-child(1) {
    margin-left: 10vw;
  }

  #num_keys .num:nth-child(4) {
    margin-left: 10vw;
  }

  #num_keys .num:nth-child(7) {
    margin-left: 10vw;
  }

  #num_keys .num:nth-child(10) {
    margin-left: 10vw;
  }

  /*结束按钮*/
  #allBtn {
    width: 100%;
    height: 85%;
    text-align: center;
    position: absolute;
    margin-top: 12vw;
  }

  #allBtn .ending1 {
    width: 14vw;
    height: 14vw;
    background-color: #a3a3a3;
    /*margin: 5vw 0 0 8%;*/
    border-radius: 15vw;
    position: fixed;
    top: 10vh;
    left: 8vw;
    box-shadow: 0 0 10px -5px #4a4a4a;
  }

  #allBtn .ending2 {
    width: 12vw;
    height: 12vw;
    background-image: linear-gradient(to bottom, #f3f3f3, #b9babd);
    border-radius: 15vw;
    position: absolute;
    bottom: 50%;
    left: 50%;
    transform: translateX(-6vw) translateY(6vw);
    line-height: 13vw;
    font-size: 6vw;
  }

  /*音量控制*/
  #allBtn .volume1 {
    width: 40vw;
    height: 10vw;
    text-align: center;
    line-height: 10vw;
    position: fixed;
    top: 11vh;
    left: 50%;
    transform: translateX(-20vw);
    border-radius: 5vw;
    background-color: #a3a3a3;
  }

  #allBtn .volume2 {
    width: 39vw;
    height: 9vw;
    text-align: center;
    line-height: 9vw;
    font-size: 4vw;
    color: #8b8b8b;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translateX(-19.5vw) translateY(-4.5vw);
    border-radius: 5vw;
    background-image: linear-gradient(to bottom, #f3f3f3, #b9babd);
  }

  #allBtn .volSub {
    position: absolute;
    top: 2.5vw;
    left: 5vw;
    font-size: 6vw;
    color: #ff7f00;
  }

  #allBtn .volAdd {
    position: absolute;
    top: 2.5vw;
    right: 5vw;
    font-size: 6vw;
    color: #ff7f00;
  }

  /*菜单按钮*/
  #allBtn .menu1 {
    width: 14vw;
    height: 14vw;
    background-color: #a3a3a3;
    /*margin: 5vw 0 0 8%;*/
    border-radius: 15vw;
    position: fixed;
    top: 10vh;
    right: 8vw;
    box-shadow: 0 0 10px -5px #4a4a4a;
  }

  #allBtn .menu2 {
    width: 12vw;
    height: 12vw;
    background-image: linear-gradient(to bottom, #f3f3f3, #b9babd);
    border-radius: 15vw;
    position: absolute;
    bottom: 50%;
    left: 50%;
    transform: translateX(-6vw) translateY(6vw);
    line-height: 12vw;
    font-size: 4vw;
  }

  /*大圆盘*/
  /*圆盘*/
  #allBtn .disk1 {
    width: 60vw;
    height: 60vw;
    position: fixed;
    top: 20vh;
    left: 50%;
    transform: translateX(-30vw);
    border-radius: 30vw;
    background-image: linear-gradient(to bottom, white 45%, #565656);
  }

  #allBtn .disk2 {
    width: 59vw;
    height: 59vw;
    /*background-color: #dfdfdf;*/
    position: absolute;
    top: 0.5vw;
    left: 0.5vw;
    border-radius: 30vw;
    background-image: linear-gradient(to bottom, #d8d8d8 45%, #ffffff);
  }

  #allBtn .s_disk1 {
    width: 25vw;
    height: 25vw;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translateX(-12.5vw) translateY(-12.5vw);
    border-radius: 12.5vw;
    background-image: linear-gradient(to bottom, #a4a4a5, #FFFFFF 60%);
  }

  #allBtn .s_disk2 {
    width: 24vw;
    height: 24vw;
    position: absolute;
    top: 0.5vw;
    left: 0.5vw;
    border-radius: 12vw;
    background-image: linear-gradient(to bottom, #F5F6F6, #cbcdcf);
    text-align: center;
    line-height: 24vw;
    font-size: 6vw;
    color: #ff7f00;
  }

  /*圆盘里的箭头*/
  #allBtn .up {
    width: 10vw;
    height: 10vw;
    font-size: 8vw;
    color: #ff7f00;
    position: absolute;
    top: 2vw;
    left: 50%;
    transform: translateX(-5vw);
  }

  #allBtn .down {
    width: 10vw;
    height: 10vw;
    font-size: 8vw;
    color: #ff7f00;
    position: absolute;
    bottom: 2vw;
    left: 50%;
    transform: translateX(-5vw) rotate(180deg);
  }

  #allBtn .left {
    width: 10vw;
    height: 10vw;
    font-size: 8vw;
    color: #ff7f00;
    position: absolute;
    top: 50%;
    left: 2vw;
    transform: translateY(-5vw) rotate(-90deg);
  }

  #allBtn .right {
    width: 10vw;
    height: 10vw;
    font-size: 8vw;
    color: #ff7f00;
    position: absolute;
    top: 50%;
    right: 2vw;
    transform: translateY(-5vw) rotate(90deg);
  }

  /*退出按钮*/
  #allBtn .exit1 {
    width: 14vw;
    height: 14vw;
    background-color: #a3a3a3;
    border-radius: 15vw;
    position: fixed;
    top: 62vh;
    left: 13vw;
    box-shadow: 0 0 10px -5px #4a4a4a;
  }

  #allBtn .exit2 {
    width: 12vw;
    height: 12vw;
    background-image: linear-gradient(to bottom, #f3f3f3, #b9babd);
    border-radius: 15vw;
    position: absolute;
    bottom: 50%;
    left: 50%;
    transform: translateX(-6vw) translateY(6vw);
    line-height: 12vw;
    font-size: 4vw;
    /*color: #ff7f00;*/
  }

  /*输入文字按钮*/
  #allBtn .channel1 {
    width: 35vw;
    height: 10vw;
    text-align: center;
    line-height: 10vw;
    position: fixed;
    top: 64vh;
    left: 50%;
    transform: translateX(-17.5vw);
    border-radius: 5vw;
    background-color: #a3a3a3;
  }

  #allBtn .channel2 {
    width: 34vw;
    height: 9vw;
    text-align: center;
    line-height: 9vw;
    font-size: 4vw;
    color: #8b8b8b;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translateX(-17vw) translateY(-4.6vw);
    border-radius: 5vw;
    border: 0;
    background-image: linear-gradient(to bottom, #f3f3f3, #b9babd);
  }

  /*返回按钮*/
  #allBtn .back1 {
    width: 14vw;
    height: 14vw;
    background-color: #a3a3a3;
    border-radius: 15vw;
    position: fixed;
    top: 62vh;
    right: 13vw;
    box-shadow: 0 0 10px -5px #4a4a4a;
  }

  #allBtn .back2 {
    width: 12vw;
    height: 12vw;
    background-image: linear-gradient(to bottom, #f3f3f3, #b9babd);
    border-radius: 15vw;
    position: absolute;
    bottom: 50%;
    left: 50%;
    transform: translateX(-6vw) translateY(6vw);
    line-height: 12vw;
    font-size: 4vw;
    /*color: #ff7f00;*/
  }

  /*分割线*/
  #allBtn .cut_line {
    display: block;
    position: fixed;
    width: 100%;
    height: 1px;
    background-color: #d8d8d8;
    top: 77vh;
  }

  /*区域分割线*/
  #allBtn .area_line1 {
    display: block;
    width: 1px;
    height: 25vh;
    background-color: #d8d8d8;
    position: absolute;
    top: 62vh;
    left: 50%;
  }

  #allBtn .area_line2 {
    display: block;
    width: 100%;
    height: 1px;
    background-color: #d8d8d8;
    position: absolute;
    top: 72.5vh;
  }

  /*小图标*/
  /*icon1*/
  #allBtn .icon-chs_sywx_ic_detail_livez {
    font-size: 8vw;
    position: absolute;
    top: 64vh;
    color: #6e6e6e;
    left: 15vw;
  }

  #allBtn .icon1 {
    width: 20vw;
    height: 12vw;
    font-size: 3vw;
  }

  /*icon2*/
  #allBtn .icon-chs_sywx_ic_detail_vodz {
    font-size: 8vw;
    position: absolute;
    top: 64vh;
    color: #6e6e6e;
    right: 15vw;
  }

  #allBtn .icon2 {
    width: 20vw;
    height: 12vw;
    font-size: 3vw;
  }

  /*icon3*/
  #allBtn .icon-chs_sywx_ic_detail_num {
    font-size: 10vw;
    position: fixed;
    top: 80vh;
    color: #6e6e6e;
    left: 40vw;
  }

  #allBtn .icon3 {
    width: 20vw;
    height: 12vw;
    font-size: 4vw;
  }

  /*icon4*/
  #allBtn .icon-chs_sywx_ic_detail_setting {
    font-size: 8vw;
    position: absolute;
    top: 74.5vh;
    color: #6e6e6e;
    right: 15vw;
  }

  #allBtn .icon4 {
    width: 20vw;
    height: 12vw;
    font-size: 3vw;
  }

  /*被点样式汇总*/
  .isActiveA1 {
    /*box-shadow: 0 0 10px 0px #4a4a4a !important;*/
    color: #FF3E05 !important;
  }

  .isActiveA2 {
    /*background-image: linear-gradient(to bottom, #ffdc8f, #ff822f) !important;*/
    /*color: white !important;*/
    background-image: linear-gradient(to bottom, #b9babd, #f3f3f3) !important;
  }

  .isActiveB {
    /*color: #FF3E05 !important;*/
  }

  .isActiveC {
    color: #fff100 !important;
  }

</style>
