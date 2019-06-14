<template>
  <div id="userLayout" :class="['user-layout-wrapper', device]">
    <div class="container">
      <!-- header -->
      <div class="header">
        <!-- logo -->
        <div class="logo">
          <a href="/">
            <img src="@/assets/img/login/logo.png" alt="logo">
          </a>
        </div>

        <!-- title-list -->
        <ul class="title-list">
          <router-link to="/user/login">
            <li v-for="(page,index) in pagelists" :key="index" class="title">{{page.title}}</li>
          </router-link>
        </ul>

        <a class="login" href="/user/login">
          <span>请登录</span>
          <img src="@/assets/img/login/login.png">
        </a>
      </div>

      <!-- body -->
      <div class="body">
        <route-view></route-view>
      </div>

      <!-- footer -->
      <!-- <div class="footer">
          <div class="links"></div>
          <div class="copyright">Copyright &copy; 2019 建管部研发小组</div>
      </div>-->
    </div>
  </div>
</template>

<script>
import RouteView from '@/components/layouts/RouteView'
import { mixinDevice } from '@/utils/mixin.js'

export default {
  name: 'UserLayout',
  components: { RouteView },
  mixins: [mixinDevice],
  data() {
    return {
      pagelists: [
        {
          title: '统计报表'
        },
        {
          title: '系统管理'
        },
        {
          title: '系统监控'
        },
        {
          title: '消息中心'
        },
        {
          title: '常见案例'
        },
        {
          title: '结果页'
        },
        {
          title: '详情页'
        },
        {
          title: '异常页'
        },
        {
          title: '个人页'
        },
        {
          title: '列表页'
        },
        {
          title: '表单页'
        }
      ]
    }
  },
  mounted() {
    document.body.classList.add('userLayout')
  },
  beforeDestroy() {
    document.body.classList.remove('userLayout')
  }
}
</script>

<style lang="scss" scoped>
#userLayout.user-layout-wrapper {
  height: 100%;

  &.mobile {
    .container {
      .main {
        max-width: 368px;
        width: 98%;
      }
    }
  }

  .container {
    width: 100%;
    min-height: 100%;
    background: #f0f2f5 url(~@/assets/img/login/background.png) no-repeat 50%;
    background-size: 100% 100%;
    padding: 58px 80px 0 80px;
    position: relative;

    .header {
      display: flex;
      align-items: center;
      .logo {
        padding-right: 60px;
      }
      .title-list {
        flex-grow: 1;
        .title {
          float: left;
          margin-right: 48px;
          height: 14px;
          font-size: 13px;
          font-family: MicrosoftYaHei;
          font-weight: 400;
          color: rgba(255, 255, 255, 1);
          line-height: 32px;
          opacity: 0.66;
        }
      }
      .login {
        display: flex;
        align-items: center;
        justify-content: space-between;

        span {
          font-size: 14px;
          font-family: SourceHanSansCN-Regular;
          font-weight: 400;
          color: rgba(255, 255, 255, 1);
          line-height: 32px;
          opacity: 0.66;
        }

        img {
          width: 12px;
          height: 13px;
          opacity: 0.66;
          margin-left: 5px;
        }
      }
    }
  }
}
</style>