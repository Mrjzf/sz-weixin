/**
 * Created by lt on 2017/6/22.
 */
import store from  "../vuex"
import '@/assets/js/strophe'
import '@/assets/js/strophe.register'
import router from '../router'
import {Toast} from 'mint-ui';
import { MessageBox } from 'mint-ui';

const keyMap = {
  "1": "-16777213",
  "KEY_STOP": "-16777193",
  "KEY_GUIDE": "-16777192",
  "KEY_PAGEUP": "-16777191",
  "KEY_PAGEDOWN": "-16777190",
  "KEY_VOLUME_UP": "-16777189",
  "KEY_POWER": "-16777215",
  "2": "-16777212",
  "3": "-16777211",
  "4": "-16777210",
  "5": "-16777209",
  "6": "-16777208",
  "7": "-16777207",
  "8": "-16777206",
  "9": "-16777205",
  "0": "-16777204",
  "KEY_MENU": "-16777203",
  "KEY_QUIT": "-16777202",
  "KEY_BACK": "-16777201",
  "KEY_FAVORITE": "-16777200",
  "KEY_OK": "-16777199",
  "KEY_LEFT": "-16777198",
  "KEY_UP": "-16777197",
  "KEY_DOWN": "-16777196",
  "KEY_RIGHT": "-16777195",
  "KEY_PLAY": "-16777194",
  "KEY_VOLUME_DOWN": "-16777188",
  "KEY_CHANNEL_UP": "-16777187",
  "KEY_CHANNEL_DOWN": "-16777186",
  "KEY_TV": "-16777185",
  "KEY_RADIO": "-16777184",
  "KEY_VOD": "-16777183",
  "KEY_INFORMATION": "-16777182",
  "KEY_TIP": "-16777181",
  "KEY_TAB": "-16777180",
  "KEY_RED": "-16777179",
  "KEY_BLUE": "-16777178",
  "KEY_GREEN": "-16777177",
  "KEY_YELLOW": "-16777176",
  "KEY_RECALL": "-16777175",
  "KEY_DASSD": "-16777174",
  "KEY_DASSV": "-16777173",
  "KEY_DASSN": "-16777172",
  "KEY_FORWARD": "-16777171",
  "KEY_BACKWARD": "-16777170",
  "KEY_TRACK": "-16777169",
  "KEY_MAIL": "-16777168"
}
const HOST = "ws://122.193.8.100:7076/websocket";
const DOMAIN = "dp0.sz96296.com";


export default {
  connection: null,
  status: {openid_err: "0", success: "200"},
  toast: null,
  msg:"",
  created(){
    var  _this=this;
    document.addEventListener("visibilitychange", function () {
      if (document.visibilityState == 'visible' && _this.connection != null && !_this.connection.connected) {
        _this.checkConnect()
      }
    });
  },
  checkConnect(cb) {

    if (store.state.openid == 0 || store.state.openid == undefined || store.state.openid == -1) {
      if (this.msg == "other") {
        store.commit("isSmallRCshow", true);
        store.commit("isRCshow", false);
      } else {
        if (this.msg == "mobile") {
          store.commit("isSmallRCshow", false);
          store.commit("isRCshow", false);
          return
        }
        if (store.state.isSmallRCshow == false) {
          store.commit("isSmallRCshow", !store.state.isSmallRCshow);
        }
        store.commit("isRCshow", false);
      }

      if (cb) {
        cb(this.status.openid_err)
      }
    } else if (store.state.STBid == "") {
      MessageBox.alert('请先绑定机顶盒哦!', '温馨提示').then(action => {
        router.push({path: "/STB_list"});
      });
    } else {
      this.toast = Toast({
        message: '连接中,请稍等...',
        position: 'middle',
        duration: -1
      });
      this.connection = new Strophe.Connection(HOST);
      var username = store.state.openid;
      var password = "123456";

      this.connection.register.connect(DOMAIN, (status) => {

        if (status === Strophe.Status.REGISTER) {
          this.connection.register.fields.username = username;
          this.connection.register.fields.password = password;
          this.connection.register.submit();
        } else if (status === Strophe.Status.REGISTERED) {
          this.connection.authenticate();
        } else if (status === Strophe.Status.CONNECTED) {
          this.toast.close();
          Toast({
            message: '连接成功!',
            duration: 1000
          });
          if (cb) {
            cb(this.status.success);
          }
        } else if (status === Strophe.Status.ERROR) {
          this.toast.close();
          Toast({
            message: '连接发生错误!',
            duration: 1000
          });
        }
      });
    }
  },
  sendMsg(msg, o, cb) {

    if (this.connection == null || !this.connection.connected) {
      this.msg = msg;
      this.checkConnect( (s) => {
        switch (s){
          case this.status.success:
            this.send(msg, o);
            break
          case this.status.openid_err:
            cb(s)
            break
        }

      })
    } else {
      this.send(msg, o);
    }

    if (msg == "review" || msg == "package" || msg == "movie") {
      if (store.state.STBid == "") {
        return
      } else {
        store.commit("isSmallRCshow", !store.state.isSmallRCshow);
        store.commit("isRCshow", !store.state.isRCshow);
      }
    }

  },
  send(msg, o){
    var to = store.state.STBid + "@" + DOMAIN;
    var from = store.state.openid + "@" + DOMAIN;
    var con = "";

    switch (msg) {
      case "live":
      case "review":
        con = o;
        break;
      case "package":
      case "movie":
        con = '<SetAVTransportURI><InstanceID>0</InstanceID><CurrentURI>http://172.18.224.25/vpg/forsearchindex.do?appid=mobile&hd=y&movieassetid=' + o.ID + '</CurrentURI><CurrentURIMetaData>mediaType=4&Freq=0&Symb=0&Mod=0&ServiceID=0&RangeStart=0&RangeEnd=0&Volume=30&PlayOffset=48</CurrentURIMetaData></SetAVTransportURI>'
        break;
      case "message":
        con = "<InputMethod><InstanceID>0</InstanceID><Unit>InputText</Unit><Target>" + o + "</Target></InputMethod>";
        break;
      default:
        con = "<Keyboard><InstanceID>0</InstanceID><Unit>KEYBOARD_CODE</Unit><Target>" + keyMap[msg] + "</Target></Keyboard>"
        break;
    }

    var m = $msg({
      to: to, //发送过去的地址(机顶盒号id+域名)
      from: from, //本地地址
      type: 'chat'
    }).c("body").t(con);

    this.connection.send(m);
  },
}
