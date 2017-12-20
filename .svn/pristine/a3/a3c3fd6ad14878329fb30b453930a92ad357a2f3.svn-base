<template>
  <div id="VOD_Category_Con">

    <header>
      <span class="icon-chs_sywx_ic_jt_left" @click="goback();"></span>
      {{ title }}
      <span class="icon-chs_sywx_ic_search" @click="gosearch();"></span>
    </header>

    <div id="middleHeight"></div>

    <div id="options" v-show="$route.query.words == 'all'">

      <div class="option_son">
        <span>地区</span>
        <div class="swiper-container">
          <div class="swiper-wrapper">
            <div :class="'swiper-slide' | isActive(index, $store.state.areaActive)" v-for="(item, index) in areaList" @click="isAreaChange(index, item.conditionCode)">
              {{ item.conditionValue }}
            </div>
          </div>
        </div>
      </div>

      <div class="option_son">
        <span>年份</span>
        <div class="swiper-container">
          <div class="swiper-wrapper">
            <div :class="'swiper-slide' | isActive(index, $store.state.yearActive)" v-for="(item, index) in yearList" @click="isYearChange(index, item.conditionCode)">
              {{ item.conditionValue }}
            </div>
          </div>
        </div>
      </div>

      <div class="option_son">
        <span>类型</span>
        <div class="swiper-container">
          <div class="swiper-wrapper">
            <div :class="'swiper-slide' | isActive(index, $store.state.typeActive)" v-for="(item, index) in genreList" @click="isTypeChange(index, item.conditionCode)">
              {{ item.conditionValue }}
            </div>
          </div>
        </div>
      </div>

      <div class="option_son">
        <span>终端</span>
        <div class="swiper-container">
          <div class="swiper-wrapper">
            <div :class="'swiper-slide' | isActive(index, $store.state.terminalActive)" v-for="(item, index) in terminalList" @click="isTerminalChange(index, item.conditionCode)">
              {{ item.conditionValue }}
            </div>
          </div>
        </div>
      </div>

    </div>

    <mt-loadmore :bottomMethod="getNext"
                 @bottom-status-change="handleTopChange"
                 :bottom-all-loaded="allLoaded"
                 ref="loadmore">
      <div>
        <Category_Con :data="conList"></Category_Con>
      </div>

      <div slot="bottom" class="mint-loadmore-bottom" v-show="pageisOK">
        <div v-show="bottomStatus !== 'loading'" >
          <span :class="{ 'rotate': bottomStatus === 'drop' }">↑</span>
          <span v-if="bottomStatus === 'pull'">&nbsp;上拉加载</span>
          <span v-else>&nbsp;释放加载</span>
        </div>
        <span v-show="bottomStatus === 'loading'">Loading...</span>
      </div>
      <div id="dataEnd" v-show="allLoaded">没有更多了！</div>
    </mt-loadmore>

    <!--<div id="footerHeight"></div>-->

  </div>
</template>

<script>
  import Category_Con from '@/components/VOD/VOD_Category_Con/Category_Con.vue'

  export default {
    data(){
      return {
        isAll: true,
        title: "",
        areaList: [],
        yearList: [],
        genreList: [],
        terminalList: [],
        conList: [],
        pageisOK: false,
        bottomStatus: "",
        allLoaded: false,
        totalPage: 0,
        pageNum: 1,
        chaxunType: ""
      }
    },
    created(){
      this.optionIsShow();
      this.getMovieFilter();
      this.getConByFilter(this.$store.state.region, this.$store.state.year, this.$route.query.words=="all"?this.$store.state.genre:this.$route.query.words, this.$store.state.terminal);
    },
    mounted(){
      this.Swiper();
    },
    methods: {
      handleTopChange(Status) {
        this.bottomStatus = Status;
      },
      Swiper(){
        new Swiper('.swiper-container', {
          slidesPerView: 5,
          paginationClickable: true,
          spaceBetween: 5,
          freeMode: true,
          observer: true
        });

      },
      isAreaChange(index, v){
        this.$store.commit("areaActive", index);
        if (this.$store.state.region == v) {
          return
        }
        this.$store.commit("region", v);
        this.pageNum = 1;
        this.getConByFilter(this.$store.state.region, this.$store.state.year, this.$store.state.genre, this.$store.state.terminal);
      },
      isYearChange(index, v){
        this.$store.commit("yearActive", index);
        if (this.$store.state.year == v) {
          return
        }
        this.$store.commit("year", v);
        this.pageNum = 1;
        this.getConByFilter(this.$store.state.region, this.$store.state.year, this.$store.state.genre, this.$store.state.terminal);
      },
      isTypeChange(index, v){
        this.$store.commit("typeActive", index);
        if (this.$store.state.genre == v) {
          return
        }
        this.$store.commit("genre", v);
        this.pageNum = 1;
        this.getConByFilter(this.$store.state.region, this.$store.state.year, this.$store.state.genre, this.$store.state.terminal);
      },
      isTerminalChange(index, v){
        this.$store.commit("terminalActive", index);
        if (this.$store.state.terminal == v) {
          return
        }
        this.$store.commit("terminal", v);
        this.pageNum = 1;
        this.getConByFilter(this.$store.state.region, this.$store.state.year, this.$store.state.genre, this.$store.state.terminal);
      },
      optionIsShow(){
        var typeName = this.$route.params.typeName;
        if (typeName == "all" || typeName == "jujiall") {
          this.isAll = true;
        } else {
          this.isAll = false;
        }
      },
      getMovieFilter() {
        var _vue = this;
        if (_vue.$route.query.isDY == "DY") {
          _vue.chaxunType = "movie";
          _vue.title = "全部电影";
          var url = "/cms/thirdPartyPortalInterfaceV2/getFilterCondition.service?categoryBizType=DY";
          _vue.$http.get(url).then(res => {
            var data = res.body.typeList;
            _vue.areaList = data[0].conditionList;
            _vue.yearList = data[1].conditionList;
            _vue.yearList[_vue.yearList.length - 1].conditionValue = "更早";
            _vue.genreList = data[2].conditionList;
            _vue.terminalList = data[3].conditionList;
            _vue.areaList.unshift({conditionCode: "", conditionValue: "全部"});
            _vue.yearList.unshift({conditionCode: "", conditionValue: "全部"});
            _vue.genreList.unshift({conditionCode: "", conditionValue: "全部"});
            _vue.terminalList.unshift({conditionCode: "", conditionValue: "全部"});
          }, res => {
            console.log("错误!" + res);
          })
        }
        else {
          _vue.chaxunType = "package";
          _vue.title = "全部剧集";
          var url = "/cms/thirdPartyPortalInterfaceV2/getFilterCondition.service?categoryBizType=DSJ";
          _vue.$http.get(url).then(res => {
            var data = res.body.typeList;
            _vue.areaList = data[0].conditionList;
            _vue.yearList = data[1].conditionList;
            _vue.yearList[_vue.yearList.length - 1].conditionValue = "更早";
            _vue.genreList = data[2].conditionList;
            _vue.terminalList = data[3].conditionList;

            _vue.areaList.unshift({conditionCode: "", conditionValue: "全部"});
            _vue.yearList.unshift({conditionCode: "", conditionValue: "全部"});
            _vue.genreList.unshift({conditionCode: "", conditionValue: "全部"});
            _vue.terminalList.unshift({conditionCode: "", conditionValue: "全部"});
          }, res => {
            console.log("错误!" + res);
          })
        }

//        else if (_vue.$route.params.typeName == "xiju") {
//          _vue.title = "喜剧电影";
//          var url = "/cms/thirdPartyPortalInterfaceV2/searchByFilterCondition.service?type=movie&pageSize=15&genre=movie-comedy";
//          _vue.$http.get(url).then(res => {
//            var data = res.body;
//            _vue.conList = data.videoList;
//
//            return
//          }, res => {
//            console.log("错误!" + res);
//          })
//        }
//        else if (_vue.$route.params.typeName == "dongzuo") {
//          _vue.title = "动作电影";
//          var url = "/cms/thirdPartyPortalInterfaceV2/searchByFilterCondition.service?type=movie&pageSize=15&genre=movie-action";
//          _vue.$http.get(url).then(res => {
//            var data = res.body;
//            _vue.conList = data.videoList;
//          }, res => {
//            console.log("错误!" + res);
//          })
//        }
//        else if (_vue.$route.params.typeName == "kehuan") {
//          _vue.title = "科幻电影";
//          var url = "/cms/thirdPartyPortalInterfaceV2/searchByFilterCondition.service?type=movie&pageSize=15&genre=movie-science fiction";
//          _vue.$http.get(url).then(res => {
//            var data = res.body;
//            _vue.conList = data.videoList;
//          }, res => {
//            console.log("错误!" + res);
//          })
//        }
//        else if (_vue.$route.params.typeName == "guzhuang") {
//          _vue.title = "古装剧";
//          var url = "/cms/thirdPartyPortalInterfaceV2/searchByFilterCondition.service?type=package&pageSize=15&genre=series-ancient";
//          _vue.$http.get(url).then(res => {
//            var data = res.body;
//            _vue.conList = data.videoList;
//          }, res => {
//            console.log("错误!" + res);
//          })
//        }
//        else if (_vue.$route.params.typeName == "jiating") {
//          _vue.title = "家庭剧";
//          var url = "/cms/thirdPartyPortalInterfaceV2/searchByFilterCondition.service?type=package&pageSize=15&genre=series-family";
//          _vue.$http.get(url).then(res => {
//            var data = res.body;
//            _vue.conList = data.videoList;
//          }, res => {
//            console.log("错误!" + res);
//          })
//        }
//        else if (_vue.$route.params.typeName == "aiqing") {
//          _vue.title = "爱情剧";
//          var url = "/cms/thirdPartyPortalInterfaceV2/searchByFilterCondition.service?type=package&pageSize=15&genre=series-romance";
//          _vue.$http.get(url).then(res => {
//            var data = res.body;
//            _vue.conList = data.videoList;
//          }, res => {
//            console.log("错误!" + res);
//          })
//        }
      },
      getConByFilter(_region, _year, _genre, _terminal){
        this.pageNum = 1;
        this.allLoaded = false;
        var _vue = this;
        var region = "";
        var year = "";
        var genre = "";
        var terminal = "";

        if (_region == 0 || _region == undefined) {
          region = "";
        } else {
          region = _region;
        }
        if (_year == 0 || _year == undefined) {
          year = "";
        } else {
          year = _year;
        }
        if (_genre == 0 || _genre == undefined) {
          genre = "";
        } else {
          genre = _genre;
        }
        if (_terminal == 0 || _terminal == undefined) {
          terminal = "";
        } else {
          terminal = _terminal;
        }

//        if (this.$route.query.words == "xiju") {
//          genre = "movie-comedy";
//          _vue.chaxunType = "movie";
//        } else if (this.$route.query.words == "dongzuo") {
//          genre = "movie-action";
//          _vue.chaxunType = "movie";
//        } else if (this.$route.query.words == "kehuan") {
//          genre = "movie-science fiction";
//          _vue.chaxunType = "movie";
//        } else if (this.$route.query.words == "guzhuang") {
//          genre = "series-ancient";
//          _vue.chaxunType = "package";
//        } else if (this.$route.query.words == "jiating") {
//          genre = "series-family";
//          _vue.chaxunType = "package";
//        } else if (this.$route.query.words == "aiqing") {
//          genre = "series-romance";
//          _vue.chaxunType = "package";
//        }

        if (this.$route.query.isDY == "DY") {
          this.chaxunType = "movie";
        } else {
          this.chaxunType = "package";
        }

        var url = "/cms/thirdPartyPortalInterfaceV2/searchByFilterCondition.service?type=" + this.chaxunType + "&pageSize=15&region=" + region + "&year=" + year + "&genre=" + genre + "&terminal=" + terminal;
//          var url = "/cms/thirdPartyPortalInterfaceV2/searchByFilterCondition.service?pageSize=15&region=&year=&genre=";
        _vue.$http.get(url).then(res => {
          var data = res.body;
          _vue.conList = data.videoList;
          _vue.totalPage = data.totalPage;
          if (_vue.totalPage == 0) {
            _vue.pageisOK = false;
            return
          }
          if (data.totalNum < 6) {
            _vue.pageisOK = false;
            return
          }
          document.body.scrollTop = 0;
          _vue.pageisOK = true;
        }, res => {
          console.log("错误!" + res);
        })

      },
      getNext(){
        this.pageNum += 1;
        if (this.totalPage == 0) {
          return
        }
        if (this.pageNum > this.totalPage) {
          this.allLoaded = true;
          return
        }

        var _vue = this;
        var region = this.$store.state.region;
        var year = this.$store.state.year;
        var genre = this.$route.query.words=="all"?this.$store.state.genre:this.$route.query.words;
        var terminal = this.$store.state.terminal;

//        if (_region == 0 || _region == undefined) {
//          region = "";
//        } else {
//          region = _region;
//        }
//        if (_year == 0 || _year == undefined) {
//          year = "";
//        } else {
//          year = _year;
//        }
//        if (_genre == 0 || _genre == undefined) {
//          genre = "";
//        } else {
//          genre = _genre;
//        }

        if (this.$route.params.typeName == "xiju") {
          genre = "comedy";
          _vue.chaxunType = "movie";
        } else if (this.$route.params.typeName == "dongzuo") {
          genre = "action";
          _vue.chaxunType = "movie";
        } else if (this.$route.params.typeName == "kehuan") {
          genre = "science fiction";
          _vue.chaxunType = "movie";
        } else if (this.$route.params.typeName == "guzhuang") {
          genre = "series-ancient";
          _vue.chaxunType = "package";
        } else if (this.$route.params.typeName == "jiating") {
          genre = "series-family";
          _vue.chaxunType = "package";
        } else if (this.$route.params.typeName == "aiqing") {
          genre = "series-romance";
          _vue.chaxunType = "package";
        }

        var url = "/cms/thirdPartyPortalInterfaceV2/searchByFilterCondition.service?type=" + _vue.chaxunType + "&pageNum=" + this.pageNum + "&pageSize=15&region=" + region + "&year=" + year + "&genre=" + genre + "&terminal=" + terminal;
//          var url = "/cms/thirdPartyPortalInterfaceV2/searchByFilterCondition.service?pageSize=15&region=&year=&genre=";
        _vue.$http.get(url).then(res => {
          var data = res.body;
          _vue.conList = _vue.conList.concat(data.videoList);
          this.$refs.loadmore.onBottomLoaded();
        }, res => {
          console.log("错误!" + res);
        })

      },
      goback(){
        this.$router.go(-1);
      },
      gosearch(){
        this.$router.push({path: '/search'});
      }
    },
    filters: {
      isActive(v1, v2, v3){
        return v2 == v3 ? v1 + " isactive" : v1;
      }
    },
    components: {
      Category_Con
    }
  }
</script>

<style scoped>

  #VOD_Category_Con {
    overflow: hidden;
    background-color: white;
  }

  header {
    position: fixed;
    z-index: 999;
    width: 100vw;
    height: 7vh;
    background-color: #FF3E05;
    text-align: center;
    color: white;
    line-height: 7vh;
    font-size: 2.7vh;
  }
  header .icon-chs_sywx_ic_jt_left {
    display: block;
    height: 100%;
    float: left;
    font-size: 3vh;
    line-height: 7vh;
    margin-left: 3%;
  }
  header .icon-chs_sywx_ic_search {
    display: block;
    height: 100%;
    float: right;
    font-size: 4vh;
    line-height: 7vh;
    margin-right: 3%;
  }


  #options .option_son {
    width: 100%;
    height: 40px;
    padding-left: 8px;
    border-bottom: 1px solid #dbdbdb;
  }
  #options .option_son span {
    display: block;
    height: 40px;
    line-height: 40px;
    float: left;
    font-weight: 600;
    position: absolute;
  }

  .swiper-container {
    width: 88%;
    height: 40px;
    background-color: #fff;
    text-align: center;
    float: right;
    margin-left: 8px;
    border-bottom: 1px solid #dbdbdb;
  }
  .swiper-slide {
    line-height: 40px;
    font-size: 14px;
    color: #222222;
  }

  .isactive {
    color: #FF3E05;
  }
  .mint-loadmore {
    /*margin-top: 1vh;*/
    padding: 8px 8px 0 8px;
  }
  .mint-loadmore-bottom span {
    display: inline-block;
    vertical-align: middle;
    transition: all .25s;
  }
  .rotate {
    transform: rotate(180deg);
  }
  #dataEnd {
    width: 100%;
    /*position: fixed;*/
    /*bottom: 8vh;*/
    height: 50px;
    background-color: #e3e3e3;
    text-align: center;
    line-height: 50px;
  }

  #middleHeight {
    margin-top: 7vh;
  }

  #footerHeight {
    margin-bottom: 9vh;
  }
</style>
