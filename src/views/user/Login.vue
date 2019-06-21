<template>
  <div class="login">
    <div class="desc">工程建设管理平台</div>

    <div class="main">
      <!--  -->
      <a-form :form="form" class="user-layout-login" ref="formLogin" id="formLogin">
        <a-tabs
          :activeKey="customActiveKey"
          :tabBarStyle="{ textAlign: 'left', borderBottom: 'unset' }"
          @change="handleTabClick"
        >
          <!-- 账号密码登录 -->
          <a-tab-pane key="tab1" tab="密码登录">
            <a-form-item>
              <a-input
                size="large"
                v-decorator="['username',validatorRules.username,{ validator: this.handleUsernameOrEmail }]"
                type="text"
                placeholder="请输入帐户名"
              ></a-input>
            </a-form-item>
            <a-form-item>
              <a-input
                v-decorator="['password',validatorRules.password]"
                size="large"
                type="password"
                autocomplete="false"
                placeholder="密码"
              ></a-input>
            </a-form-item>
          </a-tab-pane>

          <!-- 手机登录 -->
          <a-tab-pane key="tab2" tab="手机登录">
            <a-form-item>
              <a-input
                v-decorator="['phone',validatorRules.phone]"
                size="large"
                type="text"
                placeholder="手机号"
              ></a-input>
            </a-form-item>

            <a-row :gutter="16">
              <a-col class="gutter-row" :span="16">
                <a-form-item>
                  <a-input
                    v-decorator="['identifyCode',validatorRules.indetifyCode]"
                    size="large"
                    type="text"
                    placeholder="请输入验证码"
                  ></a-input>
                </a-form-item>
              </a-col>
              <a-col class="gutter-row" :span="8">
                <a-button
                  class="getCaptcha"
                  tabindex="-1"
                  :disabled="state.smsSendBtn"
                  @click.stop.prevent="getCaptcha"
                  v-text="!state.smsSendBtn && '获取验证码' || (state.time+' s')"
                ></a-button>
              </a-col>
            </a-row>
          </a-tab-pane>
        </a-tabs>

        <a-form-item class="form-out">
          <a-checkbox v-model="formLogin.rememberMe">自动登陆</a-checkbox>
          <router-link
            :to="{ name: 'login', params: { user: 'aaa'} }"
            class="forge-password"
            style="float: right;"
          >忘记密码</router-link>
        </a-form-item>

        <a-form-item class="login-btn">
          <!-- loginBtn控制按钮的实效状态和载入状态，为true时按钮实效，表现载入 -->
          <a-button
            type="primary"
            htmlType="submit"
            class="login-button"
            :loading="loginBtn"
            @click.stop.prevent="handleSubmit"
            :disabled="loginBtn"
          >登录</a-button>
        </a-form-item>
        <a-form-item>
          <div class="user-login-other">
            <div class="register" @click="register">
              <div>
                <img src="@/assets/img/login/arrow.png">
                注册
              </div>
            </div>
          </div>
        </a-form-item>
      </a-form>

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

      <!-- 短信两步验证 -->
      <two-step-captcha
        v-if="requiredTwoStepCaptcha"
        :visible="stepCaptchaVisible"
        @success="stepCaptchaSuccess"
        @cancel="stepCaptchaCancel"
      ></two-step-captcha>

      <!-- 部门选择 -->
      <a-modal title="登录部门选择" :width="450" :visible="departVisible" :maskClosable="false">
        <template slot="footer">
          <a-button type="primary" @click="departOk">确认</a-button>
        </template>

        <a-form>
          <a-form-item
            :labelCol="{span:4}"
            :wrapperCol="{span:20}"
            style="margin-bottom:10px"
            :validate-status="validate_status"
          >
            <a-tooltip placement="topLeft">
              <template slot="title">
                <span>您隶属于多部门，请选择登录部门</span>
              </template>
              <a-avatar style="backgroundColor:#87d068" icon="gold"/>
            </a-tooltip>
            <a-select
              @change="departChange"
              :class="{'valid-error':validate_status=='error'}"
              placeholder="请选择登录部门"
              style="margin-left:10px;width: 80%"
            >
              <a-icon slot="suffixIcon" type="gold"/>
              <a-select-option
                v-for="d in departList"
                :key="d.id"
                :value="`${d.prjCode}.${d.orgCode}`"
              >
                <span style="float: left">{{ d.departName }}</span>
                <span
                  style="float: right; color: #8492a6; font-size: 13px"
                >{{d.prjName}}{{ d.corpName }}</span>
              </a-select-option>
            </a-select>
          </a-form-item>
        </a-form>
      </a-modal>
    </div>
  </div>
</template>

<script>
//import md5 from "md5"
import api from '@/api' // 引入api接口，接口放在了api对象里
import TwoStepCaptcha from '@/components/tools/TwoStepCaptcha' //两步验证（功能待查）
import { mapActions } from 'vuex'
import { timeFix } from '@/utils/util' // 根据当前时间，判断问候语
import Vue from 'vue'
import { ACCESS_TOKEN } from '@/store/mutation-types' // token
import JGraphicCode from '@/components/cmp/JGraphicCode' // 验证码生成器
import { putAction, postAction } from '@/api/manage' // axios方法
import UserRegister from './UserRegister'
import qs from 'qs'
import { mixinDevice } from '@/utils/mixin.js'

export default {
  mixins: [mixinDevice],
  components: {
    TwoStepCaptcha,
    JGraphicCode,
    UserRegister
  },
  data() {
    return {
      customActiveKey: 'tab1', // 激活的tab的key至，此处默认设置为tab1
      loginBtn: false, //控制手机登录时“获取验证码”按钮的载入和可操作状态
      visibleRegister: false, // 控制注册组件表单的显隐

      // login type: 0 email, 1 username, 2 telephone
      loginType: 0,
      requiredTwoStepCaptcha: false,
      stepCaptchaVisible: false,
      form: this.$form.createForm(this),
      state: {
        time: 60,
        smsSendBtn: false
      },
      formLogin: {
        username: '',
        password: '',
        identifyCode: '',
        phone: '',
        rememberMe: true
      },
      validatorRules: {
        username: { rules: [{ required: true, message: '请输入用户名!', validator: 'click' }] },
        password: { rules: [{ required: true, message: '请输入密码!', validator: 'click' }] },
        phone: { rules: [{ validator: this.validateMobile }] },
        identifyCode: { rule: [{ required: true, message: '请输入验证码!' }] },
        inputCode: { rules: [{ required: true, message: '请输入验证码!' }, { validator: this.validateInputCode }] }
      },
      verifiedCode: '',
      inputCodeContent: '',
      inputCodeNull: true,

      departList: [],
      departVisible: false, // 部门选择组件显隐
      departSelected: '',
      currentUsername: '',
      validate_status: '',

      url: '/sys/verifyMessageLogin' // 短信登录接口
    }
  },
  created() {
    Vue.ls.remove(ACCESS_TOKEN) //移除浏览器storage中的token

    // update-begin- --- author:scott ------ date:20190225 ---- for:暂时注释，未实现登录验证码功能
    //      this.$http.get('/auth/2step-code')
    //        .then(res => {
    //          this.requiredTwoStepCaptcha = res.result.stepCode
    //        }).catch(err => {
    //          console.log('2step-code:', err)
    //        })
    // update-end- --- author:scott ------ date:20190225 ---- for:暂时注释，未实现登录验证码功能
    // this.requiredTwoStepCaptcha = true
  },
  methods: {
    ...mapActions(['Login', 'LoginByPhone', 'Logout']), // 引入vuex store中的Login和loginout方法，放在当前的methods中
    // handler
    // 判断登录方式是邮件还是username,邮件 0，username 1
    handleUsernameOrEmail(rule, value, callback) {
      const regex = /^([a-zA-Z0-9_-])+@([a-zA-Z0-9_-])+((\.[a-zA-Z0-9_-]{2,3}){1,2})$/
      if (regex.test(value)) {
        this.loginType = 0
      } else {
        this.loginType = 1
      }
      callback()
    },
    // 切换登录方式tab时，获取当前tab的key值，保存到customActiveKey中
    handleTabClick(key) {
      this.customActiveKey = key
      // this.form.resetFields()
    },
    // 登录：账号或者手机登录
    handleSubmit() {
      let that = this //将this指向的vue实例赋值给that
      let loginParams = {
        remember_me: that.formLogin.rememberMe
      }
      console.log('login1')
      // 使用账户密码登陆，tab的key值为tab1时，说明是账号密码登录
      if (that.customActiveKey === 'tab1') {
        //检测username和passwoord的值；其中{force: true}表示在change触发时是否再次校检
        that.form.validateFields(['username', 'password'], { force: true }, (err, values) => {
          if (!err) {
            // err为空，则执行，将账号密码防盗loginParams对象中
            loginParams.username = values.username
            //loginParams.password = md5(values.password)
            loginParams.password = values.password
            // 执行登录操作
            that
              .Login(loginParams)
              .then(res => {
                // 根据归属部门，选择处理，0提示，2选择，1则直接登录
                console.log(res)
                this.departConfirm(res)
              })
              .catch(err => {
                that.requestFailed(err)
              })
          }
        })
        // 使用手机号登陆
      } else {
        console.log('login2')
        that.form.validateFields(['phone', 'identifyCode'], { force: true }, (err, values) => {
          console.log('login3')
          if (!err) {
            console.log(values)
            console.log('phoneTest')

            loginParams = qs.stringify(Object.assign({}, values))
            console.log(loginParams)
            that.loginBtn = true
            console.log(11111)
            console.log(that.LoginByPhone())
            that
              .LoginByPhone(loginParams)
              .then(res => {
                console.log('loginbyphone')
                if (that.requiredTwoStepCaptcha) {
                  that.stepCaptchaVisible = true
                } else {
                  console.log(res)
                  this.departConfirm(res)
                }
              })
              .catch(err => {
                that.requestFailed(err)
              })
          }
        })
      }
    },
    getCaptcha(e) {
      e.preventDefault()
      let that = this

      this.form.validateFields(['phone'], { force: true }, (err, val) => {
        if (!err) {
          console.log(err)
          console.log(val)

          this.state.smsSendBtn = true
          that.formLogin.phone = val.phone
          console.log(that.formLogin.phone)

          let interval = window.setInterval(() => {
            if (that.state.time-- <= 0) {
              that.state.time = 60
              that.state.smsSendBtn = false
              window.clearInterval(interval)
            }
          }, 1000)

          let params = qs.stringify({ phone: that.formLogin.phone })

          const hide = this.$message.loading('验证码发送中..', 0)
          postAction(api.SendSms, params)
            .then(res => {
              console.log(res)
              setTimeout(hide, 2500)
              if (res.success) {
                this.$notification['success']({
                  message: '提示',
                  description: res.message + '请注意查收手机短信！',
                  duration: 4
                })
              } else {
                this.$notification['error']({
                  message: '获取失败',
                  description: res.message,
                  duration: 4
                })
              }
            })
            .catch(err => {
              setTimeout(hide, 1)
              clearInterval(interval)
              that.state.time = 60
              that.state.smsSendBtn = false
              this.requestFailed(err)
            })
        }
      })
    },
    stepCaptchaSuccess() {
      this.loginSuccess()
    },
    stepCaptchaCancel() {
      this.Logout().then(() => {
        this.loginBtn = false
        this.stepCaptchaVisible = false
      })
    },
    loginSuccess() {
      this.loginBtn = false
      this.$router.push({ name: 'dashboard' })
      console.log('开始登陆')
      this.$notification.success({
        message: '欢迎',
        description: `${timeFix()}，欢迎回来`
      })
      console.log('开始登陆')
    },
    requestFailed(err) {
      this.$notification['error']({
        message: '登录失败',
        description: ((err.response || {}).data || {}).message || err.message || '请求出现错误，请稍后再试',
        duration: 4
      })
      this.loginBtn = false
    },
    validateMobile(rule, value, callback) {
      if (!value || new RegExp(/^1([38][0-9]|4[579]|5[0-3,5-9]|6[6]|7[0135678]|9[89])\d{8}$/).test(value)) {
        callback()
      } else {
        callback('您的手机号码格式不正确!')
      }
    },
    validateInputCode(rule, value, callback) {
      if (!value || this.verifiedCode == this.inputCodeContent) {
        callback()
      } else {
        callback('您输入的验证码不正确!')
      }
    },
    generateCode(value) {
      this.verifiedCode = value.toLowerCase()
    },
    inputCodeChange(e) {
      this.inputCodeContent = e.target.value
      if (!e.target.value || 0 == e.target.value) {
        this.inputCodeNull = true
      } else {
        this.inputCodeContent = this.inputCodeContent.toLowerCase()
        this.inputCodeNull = false
      }
    },
    departConfirm(res) {
      if (res.success) {
        let multi_depart = res.result.multi_depart
        //0:无部门 1:一个部门 2:多个部门
        if (multi_depart == 0) {
          // 0 则提示
          this.loginSuccess()
          this.$notification.warn({
            message: '提示',
            description: `您尚未归属部门,请确认账号信息`,
            duration: 3
          })
        } else if (multi_depart == 2) {
          // 2 则选择
          this.departVisible = true // 归属部门组件显示
          this.currentUsername = this.form.getFieldValue('username') //获取当前username
          this.departList = res.result.departs
        } else {
          this.loginSuccess() // 只有1个部门则直接登录
        }
      } else {
        debugger
        this.requestFailed(res)
        this.Logout()
      }
    },
    departOk() {
      if (!this.departSelected) {
        this.validate_status = 'error'
        return false
      }
      let obj = {
        prjCode: this.departSelected.split('.')[0],
        orgCode: this.departSelected.split('.')[1],
        username: this.form.getFieldValue('username')
      }
      putAction('/sys/selectDepart', obj).then(res => {
        if (res.success) {
          this.departClear()
          this.loginSuccess()
        } else {
          this.requestFailed(res)
          this.Logout().then(() => {
            this.departClear()
          })
        }
      })
    },
    departClear() {
      this.departList = []
      this.departSelected = ''
      this.currentUsername = ''
      this.departVisible = false
      this.validate_status = ''
    },
    departChange(value) {
      this.validate_status = 'success'
      this.departSelected = value
    },

    /* 注册*/
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
@import '~@/assets/less/login.less';
</style>