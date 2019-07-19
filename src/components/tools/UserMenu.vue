<template>
  <div class="user-wrapper" :class="theme">
    <span class="action">
      <a class="setting" href="javascript:;" @click.stop.prevent="showdrawer">
        <a-icon type="setting"></a-icon>
        <!-- <img src="@/assets/img/setting/theme.png"> -->
      </a>
    </span>
    <setting-drawer :visible="visible" @closeDrawer="closedrawer" @opendrawer="showdrawer"></setting-drawer>
    <!-- <span class="action">
      <a class="logout_title" href="javascript:;" @click="handleLogout">
        <a-icon type="logout"/>
        <span v-if="isDesktop()">&nbsp;退出登录</span>
      </a>
    </span> -->
    <user-password ref="userPassword"></user-password>
    <header-notice class="action"/>
    <a-dropdown>
      <span class="action action-full ant-dropdown-link user-dropdown-menu">
        <a-avatar shape="square" class="avatar" :src="getAvatar()"/>
        <span v-if="isDesktop()" style="margin-left: 10px" class="nickname">{{ nickname() }}({{userInfo().departName}})</span>
      </span>
      <a-menu slot="overlay" class="user-dropdown-menu-wrapper">
        <!-- <a-menu-item key="0">
          <router-link :to="{ name: 'account-center' }">
            <a-icon type="user"/>
            <span>个人中心</span>
          </router-link>
        </a-menu-item>
        <a-menu-item key="1">
          <router-link :to="{ name: 'account-settings-base' }">
            <a-icon type="setting"/>
            <span>账户设置</span>
          </router-link>
        </a-menu-item> -->
        <a-menu-item key="2" @click="updatePassword">
          <a-icon type="setting"/>
          <span>密码修改</span>
        </a-menu-item>
        <a-menu-item key="3" @click="handleLogout">
          <a-icon type="logout"/>
          <span>退出登录</span>
        </a-menu-item>
        <!-- <a-menu-item key="2" disabled>
          <a-icon type="setting"/>
          <span>测试</span>
        </a-menu-item>
        <a-menu-divider/>
        <a-menu-item key="3">
          <a href="javascript:;" @click="handleLogout">
            <a-icon type="logout"/>
            <span>退出登录</span>
          </a>
        </a-menu-item>-->
      </a-menu>
    </a-dropdown>
  </div>
</template>

<script>
import HeaderNotice from './HeaderNotice'
import UserPassword from './UserPassword'
import SettingDrawer from '../setting/SettingDrawer'
import { mapActions, mapGetters } from 'vuex'
import { mixinDevice } from '@/utils/mixin.js'
import { USER_INFO } from '@/store/mutation-types'
import Vue from 'vue'

export default {
  name: 'UserMenu',
  mixins: [mixinDevice],
  components: {
    HeaderNotice,
    UserPassword,
    SettingDrawer
  },
  props: {
    theme: {
      type: String,
      required: false,
      default: 'dark'
    }
  },
  data() {
    return{
      visible: false
    }
  },
  methods: {
    ...mapActions(['Logout']),
    ...mapGetters(['nickname', 'avatar', 'userInfo']),
    getAvatar() {
      return window._CONFIG['imgDomainURL'] + '/' + this.avatar()
    },
    handleLogout() {
      const that = this

      this.$confirm({
        title: '提示',
        content: '真的要注销登录吗 ?',
        onOk() {
          return that
            .Logout({})
            .then(() => {
              window.location.href = '/'
              //window.location.reload()
            })
            .catch(err => {
              that.$message.error({
                title: '错误',
                description: err.message
              })
            })
        },
        onCancel() {}
      })
    },
    updatePassword() {
      let username = this.userInfo().username
      this.$refs.userPassword.show(username)
    },
    showdrawer() {
      this.visible = true
    },
    closedrawer(){
      this.visible = false
    }
  }
}
</script>

<style lang="less" scoped>
span.action{
  padding: 0;
}
.setting {
  color: inherit;
  text-decoration: none;
  svg{
    padding: 0;
  }
}
.layout .header .user-wrapper .action, .layout .top-nav-header-index .user-wrapper .action{
  display: flex;
  align-items: center;
  padding: 0 10px;
}
.action.action-full.ant-dropdown-link.user-dropdown-menu {
  /* padding: 0 80px 0 20px; */
  display: flex;
  align-items: center;
  .avatar {
    margin: 0;
  }
}
</style>