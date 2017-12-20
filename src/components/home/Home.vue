<template>
  <div class="templ">



  <router-view></router-view>

  <footer>
    <ul>
      <li><router-link to="/">首页</router-link></li>
      <li><router-link to="/live/channel">直播</router-link></li>
      <li><router-link to="/">遥控器</router-link></li>
      <li><router-link to="/my">我的</router-link></li>
    </ul>
  </footer>

  </div>
</template>

<script>



  export default {
    data: function() {
      return {

      }
    },
    created:function(){

    },
    components:{

    },
    methods:{

    }
  };

</script>

<style scoped>

li{
  display: inline-block;

}


</style>

