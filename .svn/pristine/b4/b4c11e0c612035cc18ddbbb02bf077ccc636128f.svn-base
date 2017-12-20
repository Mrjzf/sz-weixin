import Vue from 'vue'

export default {

  testUrl() {
    
  },

  liveUrl(_channelid, cb){
    var _u = "/cms/thirdPartyPortalInterface/getProgramList.service";
    var _d = new Date();
    var _params = {
      "date": _d.getUTCFullYear() + this.a0(_d.getMonth() + 1) + this.a0(_d.getDate()),
      'channelId': _channelid,
      "terminalType": "mobile",
      "resFormat": "json"
    }
    if (_channelid && _channelid != "") {
      this.req(_u, _params, (s, v) => {
        var _r = ""
        if (s != "error") {
          var _l = v.getProgramListResponse.programList.program;
          for (var i = 0; i < _l.length; i++) {
            if (Number(_l[i].status) == 1) {
              _r = "http://dp0.sz96296.com:7079/vod/" + _l[i].assetId + "?format=m3u8&targetType=mobile&df=sd&busiType=cpg"
              break
            }
          }
        }
        cb(s, _r)

      });
    } else {
      cb("error")
    }
  },

  req(_uri, _params, cb){
    Vue.http.get(_uri || DEFAULT, {params: _params || {}}).then(res => {

      if (cb) {
        cb("success", res.data);
      }
    }, res => {
      if (cb) {
        cb("error", {});
      }
      console.log("service/index.js  req-method  err:", res)
    });

  },
  a0(v){
    return Number(v) < 10 ? "0" + v : v + "";
  }
}
