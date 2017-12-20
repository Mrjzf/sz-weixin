<template>
  <div class="templ">

    <!--向head发送数据-->
    <RecordHead :torecordhead="torecordhead"></RecordHead>
    <div class="clearFixed"></div>

    <section v-for="(item1, index1) in dateList">
      <div class="time">{{ item1 }}</div>

      <ul>
        <li class="db" v-for="(item2, index2) in showList" v-show="item1==item2.dateinit">
          <img :src="'/cms/res/posters/mobile/' + item2.poster" alt='' />
          <hgroup>
            <h1>{{ item2.contentName }}</h1>
            <h1></h1>
            <h2>上次观看时间：{{ item2.playTime | formatTime }}</h2>
          </hgroup>
        </li>
      </ul>

    </section>

    <div id="footerHeight"></div>

  </div>
</template>

<script>
import RecordHead from './RecordHead.vue';
import moment from 'moment'
import {Toast} from 'mint-ui';

export default {
	data: function() {
		return {
      day:'2017/04/18 星期二',
      content:[
        {tv:'寒山寺',number:5,time:'12:00'},
        {tv:'寒山寺',number:5,time:'12:00'},
        {tv:'寒山寺',number:5,time:'12:00'},
        {tv:'寒山寺',number:5,time:'12:00'}
      ],
      torecordhead:3,
      startX:0,
      endX:0,
      changeX:0,
      startY:0,
      endY:0,
      changeY:0,
      showList: [],
      dateList: [],
      toast: null,
		}
	},
  filters: {
    formatTime: function (cla) {
      var a = moment(cla, "YYYYMMDD h:mm:ss").format().replace("T", " ");
      return a.slice(0, a.length - 6);
    }
  },
  methods:{

    //在点击屏幕开始收集坐标数据
    judge:function (e) {
      this.startX=e.changedTouches[0].clientX;
      this.startY=e.changedTouches[0].clientY;
    },
    //判断滑动方向,模仿点击事件
    toNext:function (e) {
      this.endX=e.changedTouches[0].clientX;
      this.endY=e.changedTouches[0].clientY;
      this.changeX=this.startX-this.endX;
      this.changeY=this.startY-this.endY;
      if((Math.abs(this.changeX)>25)&&this.changeX>0){
        //do nothing
      }else if((Math.abs(this.changeX)>25)&&this.changeX<0&&
        (Math.abs(this.changeX)>Math.abs(this.changeY))){
        document.getElementById('huikan').click();
      }
    },
    getRecord() {
      function unique(arr) {
        var result = [], hash = {};
        for (var i = 0, elem; (elem = arr[i]) != null; i++) {
          if (!hash[elem]) {
            result.push(elem);
            hash[elem] = true;
          }
        }
        return result;
      }

      this.toast = Toast({
        message: '加载中...',
        position: 'middle',
        duration: -1
      });

      var url = "/cms/thirdPartyPortalInterfaceV2/getPlayList.service?clientId=" + this.$store.state.userID + "&busiType=vpg";
      this.$http.get(url).then(res => {
        var data = res.body;
        this.showList = data.contentList;
        var datelist = [
//            "2017-08-01",
//            "2017-08-02",
//            "2017-08-03",
//            "2017-08-03",
        ];
        if (data.totalNum != 0) {
          for (let i = 0; i < data.contentList.length; i++) {
            let date = this.$moment(data.contentList[i].playTime, "YYYYMMDD h:mm:ss").format().replace("T", " ");
            let dateinit = date.slice(0, date.length - 6);
            this.showList[i].dateinit = dateinit.slice(0, 10);
            datelist.push(dateinit.slice(0, 10));
          }
          this.dateList = unique(datelist);
        }
        this.toast.close();
      }, res => {
        console.log("错误");
      })
    }
  },
	components:{
    RecordHead
  },
  created() {
    this.getRecord();
  }
}
</script>

<style scoped>
.clearFixed{
  height:52px;
}
.time{
  font-family: "Microsoft YaHei";
  background-color:#EDEDED;
  color:#999999;
  line-height:31px;
  font-size:13px;
  text-indent:11px;
  height:31px;
}
.db{
  position:relative;
  height: 100px;
  border-bottom: 1px solid #eee;
  background-color: #fff;
}

hgroup{
  position: absolute;
  left:87px;
  top: 18px;
}
h1{
  display: inline-block;
  font-weight: normal;
  font-size:15px;
  font-family:"Microsoft YaHei";
  color:#3C3C3C;

}
h2{
  padding-top: 20px;
  font-weight: normal;
  font-family: "Microsoft YaHei";
  font-size:14px;
  color:#999;
}


img{
  position:absolute;
  top:7px;
  left:16px;
  width:60px;
  border:1px solid #eee;
}

#footerHeight {
  margin-bottom: 50px;
}

</style>
