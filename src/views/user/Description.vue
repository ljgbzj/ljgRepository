<template>
  <div class="desc">
    <div class="title">CyberEng工程云</div>
    <div class="title title-desc">工程企业综合管理云平台</div>
    <p>云办公平台专注服务于各大数字化系统，汇集存储各大系统的信息数据。平台有大量的组件可以直接使用，提高项目的开发效率，减少开发工作量。</p>
    <router-link to="/user/login">
      <button :class="loginClass">登录</button>
    </router-link>
    <button @click="register" @mouseover="addClass" @mouseleave="removeClass" :class="registerClass">注册</button>

    <!-- 三步注册组件 -->
    <!-- 加v-if可以在关闭注册表单时清除表单数据 -->
    <user-register
      :visibleRegister="visible"
      @close="closeregister"
      v-if="visible"
    ></user-register>
  </div>
</template>

<script>
import UserRegister from './UserRegister'
import { mixinDevice } from '@/utils/mixin.js'

export default {
  mixins: [mixinDevice],
  name: 'description',
  data() {
    return {
      visible: false,  // 控制注册组件显隐
      registerClass: 'unchosed',
      loginClass: 'chosed'
    }
  },
  components: {
    UserRegister
  },
  methods: {
    // 打开注册对话框
    register() {
      this.visible = true
    },
    // 关闭注册对话框
    closeregister() {
      this.visible = false
    },
    addClass() {
      this.registerClass = 'chosed'
      this.loginClass = 'unchosed'
    },
    removeClass() {
      this.registerClass = 'unchosed'
      this.loginClass = 'chosed'
    }
  }
}
</script>
<style lang="less" scoped>
@import '~@/assets/less/description.less';
</style>


