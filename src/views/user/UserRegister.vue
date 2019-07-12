<template>
  <div class="register">
    <!-- 注册用户 -->
    <div class="register-one" v-show="step1">
      <div class="step1" v-if="device === 'desktop'">
        <!-- <img src="@/assets/img/register/step1.png"> -->
        用户注册
      </div>
      <a-form :form="form">
        <!-- 用户名 -->
        <a-form-item label="用户名" class="item">
          <a-input
            class="userName"
            placeholder="请输入用户名"
            v-decorator="['userName',{ rules:[{required: true, message: '请输入用户名'}]}]"
          ></a-input>
        </a-form-item>
        <a-form-item label="真实姓名" class="item">
          <a-input
            class="realName"
            v-decorator="['realName',{ rules:[{required: true, message: '请输入真实姓名'}]}]"
            placeholder="请输入真实姓名"
          ></a-input>
        </a-form-item>
        <!-- 手机号 -->
        <a-form-item label="手机号码" class="item">
          <a-input
            class="phoneNumber"
            v-decorator="['phone',{ rules:[{required: true, message: '请输入手机号码'}]}]"
            placeholder="请输入手机号码"
          ></a-input>
        </a-form-item>
        <!-- 图形验证码 -->
        <a-form-item label="图形验证码" class="item">
          <a-input
            class="code"
            v-decorator="['imageIdentifyCode',{ rules:[{required: true, message: '请输入图形验证码'}]}]"
            placeholder="请输入图形验证码"
          ></a-input>
          <!-- <j-graphic-code class="codePic" :contentWidth="100" :contentHeight="40"/> -->
          <div class="codePic">
            <img :src="urlimg" @click="getImgCode" />
          </div>
        </a-form-item>
        <!-- 手机验证码 -->
        <a-form-item label="手机验证码" class="item">
          <a-input
            class="code"
            v-decorator="['identifyCode',{ rules:[{required: true, message: '请输入验证码'}]}]"
            placeholder="请输入验证码"
          ></a-input>
          <a-button
            class="get-phone-code"
            :disabled="state.smsSendBtn"
            v-text="!state.smsSendBtn && '获取验证码' || (state.time+' s')"
            @click="getCode"
          >获取验证码</a-button>
        </a-form-item>
        <!-- 单选框，服务条款 -->
        <a-checkbox @change="getChecked">
          <span>我同意</span>&nbsp;
          <a href="/">服务条款</a>
        </a-checkbox>
        <!-- 提交 下一步 -->
        <div class="next-container">
          <span class="login" @click="registered">
            已有账号登录
            <img src="@/assets/img/register/left.png" />
          </span>
          <a-button type="primary" @click.stop.prevent="steptoTwo" class="one2two">注册</a-button>
          <span class="help">帮助中心</span>
        </div>
      </a-form>
    </div>

    <!-- 创建团队 -->
    <!-- <div class="register-two" v-if="step2">
      <div class="step step2" v-if="device === 'desktop'">
        <img src="@/assets/img/register/step2.png">
      </div>
      <a-form :form="form">
        <a-form-item label="团队名称" class="item">
          <a-input
            v-decorator="['teamName',{ rules:[{required: true, message: '请输入团队名称'}]}]"
            placeholder="请输入团队名称"
          ></a-input>
        </a-form-item>
        <a-form-item label="行业类别" class="item">
          <a-row>
            <a-col class="category">
              <a-select
                @change="getTeamTypeFather"
                class="team"
                v-decorator="['teamType',{ initialValue: '企业',
                  rules:[{required: true, message: '请选择行业类别'}]}]"
              >
                <a-icon slot="suffixIcon" type="caret-down"/>
                <a-select-option
                  v-for="(item, index) in industryCategory"
                  :key="index"
                >{{item.title}}</a-select-option>
              </a-select>
            </a-col>
            <a-col class="cate-item">
              <a-select @change="getTeamTypeChild">
                <a-icon slot="suffixIcon" type="caret-down"/>
                <a-select-option
                  v-for="(item, index) in industryCategory[teamTypeFather].list"
                  :key="index"
                >{{item.name}}</a-select-option>
              </a-select>
            </a-col>
          </a-row>
        </a-form-item>
        <a-form-item label="团队规模" class="item">
          <a-select
            placeholder="请选择"
            @change="getTeamScale"
            v-decorator="['teamScale',{ rules:[{required: true, message: '请选择团队规模'}]}]"
            class="scale"
          >
            <a-icon slot="suffixIcon" type="caret-down"/>
            <a-select-option value="大型企业">大型企业</a-select-option>
            <a-select-option value="中型企业">中型企业</a-select-option>
            <a-select-option value="小型企业">小型企业</a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item label="团队管理者" class="item">
          <a-input :default-value="userName" disabled></a-input>
        </a-form-item>
        <div class="next-container next-container2">
          <span class="login" @click="registered">
            已有账号登录
            <img src="@/assets/img/register/left.png">
          </span>
          <div class="button-group">
            <a-button type="primary" @click="toOne" class="left">上一步</a-button>
            <a-button type="primary" @click="toDone" class="right">下一步</a-button>
          </div>
          <span class="help">帮助中心</span>
        </div>
      </a-form>
    </div>-->
    <!-- 注册完成 -->
    <div class="register-done" v-show="step3">
      <div class="step step3" v-if="device === 'desktop'">
        <!-- <img src="@/assets/img/register/step3.png"> -->
        注册成功
      </div>
      <div class="result">
        <img src="@/assets/img/register/registerdone.png" />
        <div>恭喜您完成注册</div>
      </div>
      <div class="jump">
        <div>系统将自动跳转{{time}}s</div>
        <router-link to="/user/login">{{teamName}}</router-link>
      </div>
      <div class="desc">如果系统没有自动跳转，请点击团队名进入</div>
    </div>
  </div>
</template>

<script>
import { postAction, getActionUrl, httpActionHeader } from '@/api/manage.js'
import qs from 'qs'
import { setInterval, clearInterval } from 'timers'
import { mixinDevice } from '@/utils/mixin.js'

export default {
  mixins: [mixinDevice],
  name: 'UserRegister',
  data() {
    return {
      form: this.$form.createForm(this),
      userName: '',
      checked: '',
      teamType: '',
      teamTypeFather: 0,
      teamTypeChild: 0,
      teamScale: '',
      time: 5,
      teamName: '团队名',
      urlimg: '',
      formRegister: {
        phone: '',
        identifyCode: '',
        imageIdentifyCode: ''
      },
      url: {
        login: '/sys/sendMessage/login',
        verify: '/sys/verifyMessageLogin',
        registercode: '/sys/sendMessage/register',
        adduser: '/sys/user/addUser',
        imgCode: '/sys/image/making',
        addteam: '/test/cmpTeam/add'
      },
      step1: true,
      step2: false,
      step3: false,
      state: {
        time: 60,
        smsSendBtn: false
      },
      industryCategory: [
        {
          title: '企业',
          list: [
            {
              name: '企业1'
            },
            {
              name: '企业2'
            },
            {
              name: '企业3'
            },
            {
              name: '企业4'
            }
          ]
        },
        {
          title: '项目',
          list: [
            {
              name: '项目1'
            },
            {
              name: '项目2'
            },
            {
              name: '项目3'
            },
            {
              name: '项目4'
            }
          ]
        }
      ]
    }
  },
  created() {
    this.getImgCode()
    this.getTeamType()
  },
  computed: {},
  methods: {
    getChecked(e) {
      this.checked = e.target.checked
      console.log(this.checked)
    },
    getTeamTypeFather(typeFather) {
      this.teamTypeFather = typeFather
      console.log(this.teamTypeFather)
    },
    getTeamTypeChild(typeChild) {
      this.teamTypeChild = typeChild
      console.log(this.teamTypeChild)
    },
    getTeamType() {
      let item = this.industryCategory[this.teamTypeFather]
      this.teamType = item.title + ' ' + item.list[this.teamTypeChild].name
    },
    getTeamScale(scale) {
      this.teamScale = scale
      console.log(this.teamScale)
    },
    // 获取图形验证码
    getImgCode() {
      let url = this.url.imgCode
      getActionUrl(url).then(res => {
        // 将二进制流转换为图片路径
        this.urlimg =
          'data:image/png;base64,' +
          btoa(new Uint8Array(res).reduce((data, byte) => data + String.fromCharCode(byte), ''))
      })
    },
    // 获取手机验证码
    getCode() {
      let that = this
      // 验证phone 和mageIdentifyCode 是否填写
      this.form.validateFields(['phone', 'imageIdentifyCode'], { force: true }, (err, val) => {
        if (!err) {
          // 填写完成，将按钮设置成不可点击，倒计时60秒
          that.state.smsSendBtn = true
          let interval = window.setInterval(() => {
            if (that.state.time-- <= 0) {
              that.state.time = 60
              that.state.smsSendBtn = false
              window.clearInterval(interval)
            }
          }, 1000)

          //同时则将填写数据存入formRegister
          that.formRegister.phone = val.phone
          that.formRegister.imageIdentifyCode = val.imageIdentifyCode
          // 将参数形式转换为 JSON字符串
          let params = qs.stringify(that.formRegister)
          // 获取 API url
          let url = that.url.registercode
          // 提示信息
          const hide = this.$message.loading('验证码发送中..', 0)
          // 发送请求
          postAction(url, params)
            .then(res => {
              // 弹出提示信息 hide
              setTimeout(hide, 2500)
              // 根据返回结果，弹出提示框
              if (res.success) {
                this.$notification['success']({
                  message: '提示',
                  description: res.message + '请注意查收手机短信！',
                  duration: 8
                })
              } else {
                this.$notification['error']({
                  message: '获取失败',
                  description: res.message,
                  duration: 8
                })
              }
            })
            .catch(err => {
              setTimeout(hide, 1)
              clearInterval(interval)
              that.state.time = 60
              that.state.smsSendBtn = false
              this.$notification['error']({
                message: '获取失败',
                description: ((err.response || {}).data || {}).message || err.message || '请求出现错误，请稍后再试',
                duration: 4
              })
            })
        }
      })
    },
    registered() {
      this.$router.push('/user/login')
      let path = this.$route.path
      if (path === '/user/login') {
        this.$emit('closereg')
      }
    },
    toOne() {
      this.step1 = true
      this.step2 = false
      this.step3 = false
    },
    steptoTwo() {
      if (!this.checked) {
        this.$notification['error']({
          message: '提示',
          description: '请仔细阅读服务条款，同意后方可注册！',
          duration: 8
        })
      } else {
        this.form.validateFields(
          ['userName', 'realName', 'phone', 'identifyCode', 'imageIdentifyCode'],
          { force: true },
          (err, values) => {
            if (!err) {
              // 将输入内容存入参数对象，并转换为 JSON字符串
              this.userName = values.userName
              console.log(this.userName)
              let params = values
              console.log(params)
              // 设置 API url
              let url = this.url.adduser,
                method = 'post'

              // 用户注册提示信息
              const hide = this.$message.loading('用户注册中，请稍后..', 0) 
              // 发送用户注册请求
              httpActionHeader(url, params, method)
                .then(res => {
                  setTimeout(hide, 1000)
                  if (res.success) {
                    this.$notification['success']({
                      message: '提示',
                      description: res.message,
                      duration: 8
                    })
                    // 隐藏第一步、第三步，显示第二步：企业注册
                    this.step1 = false
                    this.step2 = false
                    this.step3 = true
                    
                    if (this.time > 1) {
                    this.startInter = setInterval(() => {
                    if (this.time > 1) {
                      this.time--
                    } else {
                      clearInterval(this.startInter)
                      this.$router.push('/')
                    }
                  }, 1000)
                }
                  } else {
                    this.$notification['error']({
                      message: '用户注册失败',
                      description: res.message,
                      duration: 8
                    })
                  }
                })
                .catch(err => {
                  setTimeout(hide, 1)
                  clearInterval(interval)
                  that.state.time = 60
                  that.state.smsSendBtn = false
                  this.$notification['error']({
                    message: '获取失败',
                    description: ((err.response || {}).data || {}).message || err.message || '请求出现错误，请稍后再试',
                    duration: 4
                  })
                }) 
            }
          }
        )
      }
    },
    toDone() {
      this.form.validateFields(['teamName', 'teamType', 'teamScale'], { force: true }, (err, values) => {
        if (!err) {
          // 将输入内容存入参数对象，并转换为 JSON字符串
          console.log(values)
          let params = values
          params = Object.assign(values, { teamManager: this.userName })
          params.teamType = this.teamType
          params = qs.stringify(params)
          console.log(params)
          // 设置 API url
          let url = this.url.addteam
          // 用户注册提示信息
          const hide = this.$message.loading('团队注册中，请稍后..', 0)
          // 发送用户注册请求
          postAction(url, params)
            .then(res => {
              setTimeout(hide, 2500)
              if (res.success) {
                this.$notification['success']({
                  message: '提示',
                  description: res.message,
                  duration: 8
                })
                // 隐藏第一步、第二步，显示第三步：注册完成
                this.step1 = false
                this.step2 = false
                this.step3 = true
                if (this.time > 1) {
                  this.startInter = setInterval(() => {
                    if (this.time > 1) {
                      this.time--
                    } else {
                      clearInterval(this.startInter)
                      this.$router.push('/')
                    }
                  }, 1000)
                }
              } else {
                this.$notification['error']({
                  message: '团队注册失败',
                  description: res.message,
                  duration: 8
                })
              }
            })
            .catch(err => {
              setTimeout(hide, 1)
              clearInterval(interval)
              that.state.time = 60
              that.state.smsSendBtn = false
              this.$notification['error']({
                message: '获取失败',
                description: ((err.response || {}).data || {}).message || err.message || '请求出现错误，请稍后再试',
                duration: 4
              })
            })
        }
      })
    }
  }
}
</script>
<style lang="less" scoped>
@import '~@/assets/less/userRegister.less';
</style>
