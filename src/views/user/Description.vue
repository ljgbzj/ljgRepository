<template>
  <div class="desc">
    <div>工程建设管理云平台</div>
    <p>云办公平台专注服务于各大数字化系统，汇集存储各大系统的信息数据。平台有大量的组件可以直接使用，提高项目的开发效率，减少开发工作量。</p>
    <router-link to="/user/login">
      <button class="login">登录</button>
    </router-link>
    <button @click="register" class="register">注册</button>

    <!-- 三步注册组件 -->
      <div>
        <a-modal
          v-model="visibleRegister"
          width="1000px"
          :footer="null"
          :class="['registerPage', device]"
          @cancel="reset"
        >
          <user-register ref="register" @closereg="closeregister"></user-register>
        </a-modal>
      </div>
  </div>
</template>

<script>

import UserRegister from './UserRegister'
import { mixinDevice } from '@/utils/mixin.js'

export default {
  mixins: [mixinDevice],
  name: 'description',
  data(){
    return{
      visibleRegister: false
    }
  },
  components: {
    UserRegister
  },
  methods: {
    register() {
      this.visibleRegister = true
    },
    reset() {
      this.$refs.register.step1 = true
      this.$refs.register.step2 = false
      this.$refs.register.step3 = false
      this.visibleRegister = false
    },
    closeregister() {
      this.visibleRegister = false
    }
  }
}
</script>
<style lang="less" scoped>
@import '~@/assets/less/description.less';
</style>


