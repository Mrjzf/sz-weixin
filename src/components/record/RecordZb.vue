<template>
  <div class="templ">

    <!--向head发送数据-->
    <RecordHead :torecordhead="torecordhead"></RecordHead>
    <div class="clearFixed"></div>

    <section v-for="(item1, index1) in dateList">
      <div class="time">{{ item1 }}</div>

      <ul>
        <li class="zb" v-for="(item2, index2) in showList" v-show="item1==item2.dateinit">
          <img :src="'/cms/' + item2.poster" alt=''/>
          <hgroup>
            <h1>{{ item2.contentName }}</h1>
            <h2>上次观看时间：{{ item2.playTime | formatTime }}<span></span></h2>
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
    data: function () {
      return {
        time: '2017/04/18 星期二',
        tv: '北京卫视',
        clarity: '高清',
        play: '甄嬛传',
        number: '32',
        torecordhead: 1,
        startX: 0,
        endX: 0,
        changeX: 0,
        startY: 0,
        endY: 0,
        changeY: 0,
        showList: [],
        dateList: [],
        toast: null,
      }
    },
    filters: {
      formatTime: function (cla) {
        var a = moment(cla, "YYYYMMDD h:mm:ss").format().replace("T", " ");
        return a.slice(0, a.length - 6);
      },
      formatNum: function (num) {
        return num + '集';
      }
    },
    computed: {

    },
    components: {
      RecordHead
    },
    methods: {

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
          document.getElementById('huikan').click();
        } else if ((Math.abs(this.changeX) > 25) && this.changeX < 0) {
          //do nothing
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

        var url = "/cms/thirdPartyPortalInterfaceV2/getPlayList.service?clientId=" + this.$store.state.userID + "&busiType=live";
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
    created() {
      this.getRecord();
    }
  };

</script>

<style scoped>
  .clearFixed {
    height: 52px;
  }

  .time {
    font-family: "Microsoft YaHei";
    background-color: #EDEDED;
    color: #999999;
    line-height: 31px;
    font-size: 13px;
    text-indent: 11px;
    height: 31px;
  }

  .zb {
    width: 100%;
    position: relative;
    height: 25vw;
    border-bottom: 1px solid #eee;
    background-color: #fff;
  }

  img {
    width: 32vw;
    position: absolute;
    top: 13%;
    left: 3%;
    border: 1px solid #eee;
  }

  hgroup {
    width: 60vw;
    position: absolute;
    left: 38%;
    top: 23%;
  }

  hgroup h1 {
    margin: 0;
    font-weight: normal;
    font-size: 16px;
    font-family: "Microsoft YaHei";
    color: #3C3C3C;

  }

  hgroup h2 {
    margin: 9px 0 0 0;
    font-weight: normal;
    font-family: "Microsoft YaHei";
    font-size: 13px;
    color: #999;
    overflow: hidden;
    text-overflow:ellipsis;
    white-space: nowrap;
  }

  #footerHeight {
    margin-bottom: 50px;
  }

</style>

