<template>

  <!--templ不是整个页面，而是由其中的内容撑开的-->
  <div class="templ">

    <div id="top">
      <header>
        <span class="icon-chs_sywx_ic_jt_left goback" @click="goback"></span>
        <div class="icon-chs_sywx_ic_search"></div>
        <input placeholder="请输入搜索内容..." v-model="key"/>
        <span class="search" @click="ajaxToSearchResult();">搜索</span>
      </header>

      <div v-show="isShow">
        <div id="record">
          <h1>搜索记录</h1>
          <p @click="clearRecord();">清空</p>
        </div>
        <div id="record-content">
          <!--v-for循环输出-->
          <p v-show="isShowHistoryNo">暂无搜索记录~</p>
          <p v-for="item in searchList"  @click="ajax2(item);">{{item}}</p>
        </div>


        <div id="hot">
          <h1>热门搜索</h1>
        </div>
        <div id="hot-content">
          <ul>
            <!--用v-for循环来输出-->
            <li v-for="(item, index) in hotShow">
          <span :class="{firstHot:index==0,secondHot:index==1,thirdHot:index==2}">
            {{ index + 1 }}
          </span>
              <p @click="ajax2(item.hotWord);">{{ item.hotWord }}</p>
            </li>
          </ul>
      </div>

      </div>
    </div>

    <!--<SearchResult v-show="isResultShow" ref="Result"></SearchResult>-->

    <div>
      <!--<div id="searchMsg">-->

      <!--<div id="vpg" v-show="search_Vpg!==[]">-->
      <!--<h1>点播列表</h1>-->
      <!--</div>-->
      <!--<div id="vpg_content" v-show="search_Vpg!==[]">-->
      <!--<div class="everyBlock" v-for="(item, index) in search_Vpg">-->
      <!--&lt;!&ndash;@click="goContent(item.id, item.type, item.aaa)"&ndash;&gt;-->
      <!--<img :src="'/cms/res/posters/mobile/' + item.poster" alt="">-->
      <!--<p class="name">{{item.contentName}}</p>-->
      <!--</div>-->
      <!--</div>-->

      <!--<div id="cpg_channel" v-show="search_Cpg_Channel!==[]">-->
      <!--<h1>频道列表</h1>-->
      <!--</div>-->
      <!--<div id="cpg_channel_content" v-show="search_Cpg_Channel!==[]">-->
      <!--<div class="everyBlock" v-for="(item, index) in search_Cpg_Channel">-->
      <!--&lt;!&ndash;@click="goContent(item.id, item.type, item.aaa)"&ndash;&gt;-->
      <!--<img :src="'/cms/' + item.poster" alt="">-->
      <!--<p class="name">{{item.contentName}}</p>-->
      <!--</div>-->
      <!--</div>-->

      <!--<div id="cpg_program" v-show="search_Cpg_Program!==[]">-->
      <!--<h1>节目列表</h1>-->
      <!--</div>-->
      <!--<div id="cpg_program_content" v-show="search_Cpg_Program!==[]">-->
      <!--<div class="everyBlock" v-for="(item, index) in search_Cpg_Program">-->
      <!--&lt;!&ndash;@click="goContent(item.id, item.type, item.aaa)"&ndash;&gt;-->
      <!--<img :src="'/cms/res/mobile/' + item.poster" alt="">-->
      <!--<p class="name">{{item.contentName}}</p>-->
      <!--</div>-->
      <!--</div>-->

      <!--</div>-->

    </div>

  </div>
</template>

<script>
  import { Toast } from 'mint-ui'

  export default {
    data: function () {
      return {
//        search_Vpg: [],
//        search_Cpg_Channel: [],
//        search_Cpg_Program: [],
        hotShow: [],
        key: "",
        isShow: true,
        searchList: [],
        isShowHistoryNo: false,
        isResultShow: false,
        toast: null
      }
    },
    components: {

    },
    created: function () {
      this.getHotShow();
      this.SearchHistory();
    },
    updated(){

    },
    methods: {
      goback: function () {
//        if (this.isShow == false) {
//          this.$router.push({path: "/search"});
//          this.searchList = [];
//        } else {
//          this.$router.go(-1);
//        }
        this.$router.go(-1);
      },
      clearRecord: function () {
        localStorage.removeItem("searchHistory");
        this.searchList = [];
        this.isShowHistoryNo = true;
      },
      getHotShow(){
        this.toast = Toast({
          message: '加载中...',
          position: 'middle',
          duration: -1
        });
        var url = "/cms/thirdPartyPortalInterface/getHotWordList.service?pageSize=10&resFormat=json";
        this.$http.get(url).then(res => {
          var data = res.body;
          this.hotShow = data.getHotWordList.hotWordList;

          this.toast.close();
        }, res => {
          this.toast.close();
          Toast("加载错误，请重新进入~")
        })
//        console.log(this);
      },
      ajaxToSearchResult(){

        this.$router.push({path: "/SearchResult", query: {key: this.key}});

      },
      ajax2(key){
        //显示搜索结果
        this.key = key;
        this.$router.push({path: "/SearchResult", query: {key: this.key}});
      },
      SearchHistory(){
        if (localStorage.searchHistory) {
          var arr = [];
          this.searchList = localStorage.getItem("searchHistory").split(",").reverse();
//          for (var i = 0; i < this.searchList.length; i++) {
//            if (this.searchList[i] !== "") {
//              arr.push(this.searchList[i])
//            }
//          }
//          this.searchList = arr;
        } else {
          this.isShowHistoryNo = true;
        }
      }
    }
  };

</script>

<style scoped>

  .templ {
    background-color: #fff;
  }

  h1, p {
    margin: 0;
    line-height: 100%;
  }

  /* 头部  */
  header {
    background-color: #FF3E05;
    height: 49px;
  }

  header input {
    height: 30px;
    line-height: 29px;
    width: 65%;
    border: 0;
    border-radius: 4px;
    margin: 8px 0 0 10%;
    position: relative;
    padding-left: 9%;
  }

  input::-webkit-input-placeholder {
    color: #999;
    font-size: 14px;
    font-family: "Microsoft YaHei";
  }
  .icon-chs_sywx_ic_jt_left {
    font-size: 16px;
  }
  .icon-chs_sywx_ic_search {
    position: absolute;
    left: 50px;
    top: 15px;
    font-size: 18px;
    color: #FF3E05;
    z-index: 9999;
  }

  header span {
    color: #fff;
    font-family: "Microsoft Yahei";
    font-size: 13px;

  }
  header .goback {
    position: absolute;
    top: 16px;
    left: 3%;
    line-height: 100%;
  }
  header .search {
    position: absolute;
    top: 18px;
    right: 4.5%;
    line-height: 100%;
  }

  /*搜索记录 */
  #record, #hot, #vpg, #cpg_channel, #cpg_program {
    background-color: #fff;
    margin-top: 8px;
    height: 48px;
    border-bottom: 1px solid #eee;
  }

  #record h1, #hot h1, #vpg h1, #cpg_channel h1, #cpg_program h1 {
    display: inline-block;
    margin: 16px 0 0 4.5%;
    font-weight: bold;
    font-size: 16px;
    font-family: "Microsoft Yahei";
  }

  #record p {
    float: right;
    margin: 14px 4.5% 0 0;
    display: inline-block;
    color: #FF3E05;
    font-size: 14px;
    font-family: "Microsoft Yahei";
  }

  #record-content {
    background-color: #fff;
    font-family: "Microsoft Yahei";
    /*height: 66px;*/
    max-height: 120px;
    color: #222222;
    font-size: 14px;
    overflow: hidden;
  }

  #record-content p {
    display: inline-block;
    width: 50%;
    padding-left: 4.5%;
    margin: 12px 0 0 0;
    font-size: 13px;
    box-sizing: border-box;
  }

  #record-content p:nth-child(3) {
    /*margin-top: 16px;*/
  }

  #record-content p:nth-child(4) {
    /*margin-top: 16px;*/
  }

  /*热门记录*/
  #hot-content {
    background-color: #fff;
    height: 200px;
    font-family: "Microsoft Yahei";
  }

  #hot-content li {
    display: inline-block;
    padding: 12px 0 0 4.5%;
    width: 45%;
  }

  #hot-content li p {
    display: inline-block;
    font-size: 13px;
    position: relative;
    top: 2px;
  }

  #hot-content li span {
    display: inline-block;
    text-align: center;
    line-height: 16px;
    margin-right: 2.2%;
    width: 16px;
    height: 16px;
    border-radius: 3px;
    font-size: 9px;
    background-color: #ccc;
    color: #fff;
  }

  #hot-content li span.firstHot {
    background-color: #FF443F;
  }

  #hot-content li span.secondHot {
    background-color: #FF903B;
  }

  #hot-content li span.thirdHot {
    background-color: #FFC535;
  }


</style>

