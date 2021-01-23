<template>
  <v-app>

    <router-view class="router-view"></router-view>
  </v-app>
</template>

<script>


export default {
  name: 'App',

  

  data: () => ({
    //
  }),
  mounted(){
    if(this.$route.name!=='Home' && !this.$store.state.isAuth){
      this.$router.push({name: 'Home'})
    }
  }
};
</script>

<style lang="scss">
.nav{
  float: left;
  min-height: 100vh;
  position: absolute;
  z-index: 2;
}
.router-view{
  min-height: 100vh;
}
h1{

  padding:20px;
  background-color: #56c3fd;
  color: #14658f;
  border: 1px solid #c4ddeb;
}
</style>
