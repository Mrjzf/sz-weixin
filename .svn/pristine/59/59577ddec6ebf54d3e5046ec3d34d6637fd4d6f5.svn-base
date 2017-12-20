<template>
    <div id="banner">

      <div class="swiper-container">
        <div class="swiper-wrapper">
          <div class="swiper-slide" v-for="(item, index) in data" :key="item.id">
            <!--v-show="item.contentType!='cms_content'"-->
            <span class="phoneMark" v-show="item.mobileScreen==1">手机看</span>
            <img :src="item.poster | imgChange" alt="" @click="goContent(item.contentId, item.showType)">
            <span v-if="item.tag!=''" class="tag">{{ item.tag }}</span>
          </div>
        </div>
        <!-- Add Pagination -->
        <div class="swiper-pagination"></div>
      </div>

    </div>
</template>

<script>
  export default {
    data(){
      return {
        mySwiper: null
      }
    },
    components: {

    },
    created(){

    },
    mounted(){
      this.Swiper();
    },
    updated(){
      this.mySwiper.update();
      this.mySwiper.reLoop();
      this.mySwiper.slideTo(1, 300, false);
    },
    props: ["data"],
    watch: {

    },
    methods: {
      Swiper(){
        this.mySwiper = new Swiper('#banner .swiper-container', {
          pagination: '#banner .swiper-pagination',
//          effect: "fade",
          autoplay: 4000,
//          autoHeight: true,
          paginationClickable: true,
          autoplayDisableOnInteraction: false,
          observer: true,
          loop: true,
        });
      },
      goContent(id, showType){
        this.$router.push({path: 'detail', query: {id: id, showType: showType}});
      }
    },
    filters: {
      imgChange(v1){
        var s = Math.random();
        return "/cms/res/mobilePoster/" + v1 + "?s=" + s;
      }
    }
  }
</script>

<style scoped>
  img {
    min-height: 156px;
  }

  #banner {
    width: 100%;
    margin-top: 0px;
  }
  #banner .swiper-pagination {
    left: 35vw;
    bottom: 5px
  }

  .tag {
    width: 100%;
    height: 25px;
    line-height: 25px;
    text-indent: 1em;
    position: absolute;
    bottom: 0;
    z-index: 999;
    color: #fff;
    background: rgba(0, 0, 0, 0.5);
  }
  .phoneMark {
    position: absolute;
    right: 0;
    width: 13%;
    height: 5vw;
    line-height: 5vw;
    text-align: center;
    font-size: 12px;
    background-color: #91cc2b;
    color: #fff;
    border-radius: 0 0 0 10px;
  }
</style>
