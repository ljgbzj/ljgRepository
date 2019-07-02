<template>
  <!-- <div class="logo">
    <router-link :to="{name:'dashboard'}">
      <img src="~@/assets/logo.svg" alt="logo">
      <h1 v-if="showTitle">{{ title }}</h1>
      </router-link>
  </div>-->
  <!-- <div class="loginContainer">
      <img src="@/assets/img/login/LOGO1.png" color="#6857FF">
      <img src="@/assets/img/login/LOGO3.png" color="#6857FF" v-if="showTitle">
  </div>-->
  <div class="logo">
    <router-link :to="{name:'dashboard'}" class="logoPic">
      <img src="@/assets/img/login/LOGO1.png">
      <img src="@/assets/img/login/LOGO3.png" v-if="showTitle">
    </router-link>
  </div>
</template>

<script>
export default {
  name: 'Logo',
  props: {
    title: {
      type: String,
      default: 'CMP 建管云',
      required: false
    },
    showTitle: {
      type: Boolean,
      default: true,
      required: false
    }
  }
}
</script>
<style lang="scss" scoped>
/*缩小首页布 局顶部的高度*/
$height: 80px;

.sider {
  box-shadow: none !important;
  .logo {
    height: $height !important;
    line-height: $height !important;
    box-shadow: none !important;
    transition: background 300ms;

    a {
      color: white;
      &:hover {
        color: rgba(255, 255, 255, 0.8);
      }
    }
  }

  /* &.light .logo {
      background-color: #6455ff;
    } */
}
// logo覆盖
/* .sider {
    box-shadow: none !important;
    .loginContainer {
      overflow: visible;
      height: $height !important;
      line-height: $height !important;
      box-shadow: none !important;
      transition: background 300ms;
      text-align: center;
    }
    &.light .loginContainer {
      background-color: #6455ff;
    }
  } */
</style>