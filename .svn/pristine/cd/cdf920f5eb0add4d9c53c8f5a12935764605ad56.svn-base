<template>
  <div id="comment">

    <!--<LiveHead :tolivehead="tolivehead"></LiveHead>-->
    <!--<div class="clearfixed"></div>-->

    <!--<router-view></router-view>-->

    <section>


      <div class="comment">
        评论({{ comments.totalNum }}条)
        <div id="dash"></div>
      </div>
      <div class="input">
        <input placeholder="我也来说几句..." v-model="liveComment"/>
        <span :class="sendComment" @click="addComment();">发送</span>
      </div>

        <ul>
          <li v-for="(item, index) in comments.commentList">
            <img :src="'http://122.193.8.107:8080/msmp/avatar/' | headimg(item.clientAvatar)" class="touxiang"/>
            <h2 class="comCon1">
              {{ item.clientAlias }}
              {{ setTime(item.createTime) | dateInit }}
            </h2>
            <p class="comCon2">
              {{ item.commentContent }}
            </p>
            <span class="comFloor">{{ index + 1 }}层</span>
            <span class="comDel" v-show="$store.state.userID==item.clientId" @click="delComment(item.id);">删除</span>
            <!--<span class="icon-chs_sywx_ic_detail_news">回复</span>-->
          </li>
        </ul>


    </section>

    <!--评论结束-->
    <div id="end">
      <p>{{ commentTips }}</p>
    </div>
    <!--<div id="footerHeight"></div>-->

  </div>
</template>

<script>
  import defultImg from '../../assets/images/defultHeadimg.png'
  import {Toast} from 'mint-ui';
  import {MessageBox} from 'mint-ui'

  export default {
    data: function () {
      return {
        commentNum: 0,
        comments1: [],
        commentTips: "",
        tolivehead: 3,
        startX: 0,
        endX: 0,
        changeX: 0,
        startY: 0,
        endY: 0,
        changeY: 0,
        liveComment: "",
        pageNum: 1,
        totalPage: 0
      }
    },
    props:["comments"],
    created(){
//      this.getComment();
    },
    filters: {
      formatCommentNum: function (num) {
        return '(' + num + '条' + ')';
      },
      formatFloor: function (num) {
        return num + 1 + '层';
      },
      headimg(v1, v2) {
        if (v2.slice(0, 4) == "http") {
          return v2;
        } else if (v2 == "default.jpg") {
          return defultImg;
        } else if (v2 == "") {
          return defultImg;
        } else {
          return v1 + v2;
        }
      },
      dateInit(v1) {
        var str = v1.replace("T", " ");
        return str.substring(0, str.indexOf("+"));
      }
    },
    computed: {
      sendComment() {
        return {
          'sendComment': this.liveComment.length == 0,
          'sendComment sendComment1': this.liveComment.length == 1,
          'sendComment sendComment2': this.liveComment.length == 2,
          'sendComment sendComment3': this.liveComment.length == 3,
          'sendComment sendComment4': this.liveComment.length == 4,
          'sendComment sendComment5': this.liveComment.length == 5,
          'sendComment sendComment6': this.liveComment.length == 6,
          'sendComment sendComment7': this.liveComment.length == 7,
          'sendComment sendComment8': this.liveComment.length == 8,
          'sendComment sendComment9': this.liveComment.length == 9,
          'sendComment sendComment10': this.liveComment.length >= 10,
        }
      }
    },
    components: {},
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
        if ((Math.abs(this.changeX) > 25) && this.changeX > 0) {
          //do nothing
        } else if ((Math.abs(this.changeX) > 25) && this.changeX < 0 &&
          (Math.abs(this.changeX) > Math.abs(this.changeY))) {
          document.getElementById('show').click();
        }
      },
      setTime(v1){
        return this.$moment(v1, "YYYYMMDD, h:mm:ss").format();
      },
      getComment(){
        //判断评论来源
        if (this.$route.name == "Detail") {
          var id = this.$route.query.id;
          var contentType = "vpg";
        }
        else if (this.$route.name == "LiveHead") {
          var id = this.$route.query.channelId;
          var contentType = "cpg_channel";
        }

        var url = "/cms/thirdPartyPortalInterfaceV2/listCommentByContent.service?contentId=" + id + "&contentType=" + contentType + "&pageSize=5";

        this.$http.get(url).then(res => {
            var data = res.body;

            this.comments1 = data.commentList;
            this.commentNum = data.totalNum;
            this.totalPage = data.totalPage;
            if (this.commentNum == 0) {
              this.commentTips = "暂无评论哦!";
            } else {
              if (data.currentPage == data.totalPage) {
                this.commentTips = "评论看完了，来一发吧!";
              }
            }

          },
          res => {
            console.log("错误" + res);
          })
      },
      addComment() {
        if (this.$store.state.openid == 0 || this.$store.state.openid == undefined || this.$store.state.openid == -1) {
          this.$parent.TVplay();
          return
        }
        if (this.$store.state.userID == 0) {
          Toast("请重新登录");
          return
        }
        if (this.liveComment == "") {
          Toast({
            message: '评论不能为空哦!',
            duration: 1500
          });
          return
        }

        //判断评论来源
        if (this.$route.name == "Detail") {
          var id = this.$route.query.id;
          var contentType = "vpg";
        }
        else if (this.$route.name == "LiveHead") {
          var id = this.$route.query.channelId;
          var contentType = "cpg_channel";
        }

        var url = "/cms/thirdPartyPortalInterfaceV2/addComment.service";

        this.$http.post(url, {
          clientId: this.$store.state.userID,
          contentId: id,
          contentType: contentType,
          commentContent: this.liveComment,
          targetType: 'mobile'
        }, {emulateJSON: true}).then(res => {
          var data = res.body;
          this.liveComment = "";
          if (data.code == 0) {
            Toast({
              message: '评论成功!',
              duration: 1500
            });
          }
          this.$emit("updateCom");
        }, res => {
          Toast("评论出错！请稍后再试！");
        })
      },
      delComment(_commentId) {
        if (this.$store.state.openid == 0 || this.$store.state.openid == undefined || this.$store.state.openid == -1) {
          this.$parent.TVplay();
          return
        }
        if (this.$store.state.userID == 0) {
          return
        }
        MessageBox.confirm('确定执行此操作?').then(action => {
          var url = "/cms/thirdPartyPortalInterfaceV2/removeComment.service?";

          this.$http.post(url, {
            clientId: this.$store.state.userID,
            commentId: _commentId
          }, {emulateJSON: true}).then(res => {
            var data = res.body;
            if (data.code == 0) {
              Toast({
                message: '删除成功!',
                duration: 1500
              });
              this.$emit("updateCom");
            }
          }, res => {
            Toast("删除失败!请稍后再试!");
          })
        });
      },

    }
  };

</script>

<style scoped>
  p {
    margin: 0;
    padding: 0;
  }

  .clearfixed {
    height: 255px;
  }

  section {
    background-color: #fff;
    overflow: hidden;
    /*margin-top: 50vh;*/
  }

  /*评论条数  */
  .comment {
    position: relative;
    height: 30px;
    font-size: 16px;
    font-family: "Microsoft YaHei";
    color: #3C3C3C;
    line-height: 30px;
    padding-left: 20px;
    border-bottom: 1px solid #eee;
  }

  .comment #dash {
    width: 4px;
    height: 19px;
    background-color: #FF3E05;
    position: absolute;
    left: 8px;
    top: 5px;
    border-radius: 4px;
  }

  /*输入框*/
  .input {
    height: 40px;
    text-align: center;
    line-height: 40px;
    border-bottom: 1px solid #eee;
    position: relative;
  }

  input {
    border: 0;
    border-radius: 41px;
    background-color: #F3F3F3;
    width: 296px;
    height: 32px;
    color: #000000;
    font-size: 14px;
    font-family: "Microsoft Yahei";
    text-align: center;
  }

  input::-webkit-input-placeholder {
    color: #858585;
    font-size: 13px;
    font-family: "Microsoft Yahei";
  }

  section .input {
    position: relative;
  }

  section .input .sendComment {
    position: absolute;
    display: block;
    width: 15vw;
    height: 32px;
    line-height: 32px;
    top: 3px;
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
    transition: all 3s !important;
    box-shadow: 0px 0px 5px 1px #000000;
  }

  /*评论详情  */
  section ul {
    overflow: hidden;
  }
  section li {
    width: 90%;
    min-height: 50px;
    margin: 0 auto;
    border-bottom: 1px solid #e1e1e1;
    overflow: hidden;
    padding-bottom: 5px;
  }

  section img {
    float: left;
    width: 10vw;
    height: 10vw;
    border-radius: 50%;
    transform: translateY(1.5vw);
  }

  section h2 {
    width: 70%;
    height: 14px;
    font-size: 12px;
    line-height: 14px;
    color: #b5b5b5;
    float: left;
    margin: 5px 0 0 10px;
  }

  section p.comCon2 {
    width: 65vw;
    float: left;
    margin: 0 0 0 10px;
  }

  section span.comFloor {
    float: right;
    color: #b5b5b5;
  }

  section span.icon-chs_sywx_ic_detail_news {
    float: right;
    transform: translateX(-10px) translateY(40px);
    color: #828282;
  }

  section span.comDel {
    color: red;
    float: right;
    /*transform: translateX(10px) translateY(20px);*/
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

  #footerHeight {
    margin-bottom: 9vh;
  }
</style>

