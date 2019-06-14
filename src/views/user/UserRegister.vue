<template>
  <div class="register">
    <!-- 注册用户 -->
    <div class="register-one" v-show="step1">
      <div class="step1">
        <img src="@/assets/img/register/step1.png">
      </div>
      <a-form :form="form">
        <!-- 用户名 -->
        <a-form-item label="用户名" class="item">
          <a-input
            class="username"
            placeholder="请输入用户名"
            v-decorator="['username',{ rules:[{required: true, message: '请输入用户名'}]}]"
          ></a-input>
        </a-form-item>
        <a-form-item label="真实姓名" class="item">
          <a-input
            class="realname"
            v-decorator="['realname',{ rules:[{required: true, message: '请输入真实姓名'}]}]"
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
            <img :src="urlimg" @click="getImgCode">
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
          <span class="login">
            已有账号登录
            <img src="@/assets/img/register/left.png">
          </span>
          <a-button type="primary" @click.stop.prevent="steptoTwo" class="one2two">下一步</a-button>
          <span class="help">帮助中心</span>
        </div>
      </a-form>
    </div>

    <!-- 创建团队 -->
    <div class="register-two" v-show="step2">
      <div class="step step2">
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
                  rules:[{required: true, message: '请输入图形验证码'}]}]"
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
            v-decorator="['teamScale',{ rules:[{required: true, message: '请输入图形验证码'}]}]"
            class="scale"
          >
            <a-icon slot="suffixIcon" type="caret-down"/>
            <a-select-option value="大型企业">大型企业</a-select-option>
            <a-select-option value="中型企业">中型企业</a-select-option>
            <a-select-option value="小型企业">小型企业</a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item label="团队管理者" class="item">
          <a-input :default-value="username" disabled></a-input>
        </a-form-item>
        <div class="next-container next-container2">
          <span class="login">
            已有账号登录
            <img src="@/assets/img/register/left.png">
          </span>
          <div>
            <a-button type="primary" @click="toOne" class="left">上一步</a-button>
            <a-button type="primary" @click="toDone" class="right">下一步</a-button>
          </div>
          <span class="help">帮助中心</span>
        </div>
      </a-form>
    </div>
    <!-- 注册完成 -->
    <div class="register-done" v-show="step3">
      <div class="step">
        <img src="@/assets/img/register/step3.png">
      </div>
      <div class="result">
        <img src="@/assets/img/register/registerdone.png">
        <div>恭喜您完成团队的创建</div>
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
import { postAction, getActionUrl } from '@/api/manage.js'
import qs from 'qs'
import { setInterval, clearInterval } from 'timers'

export default {
  name: 'UserRegister',
  data() {
    return {
      form: this.$form.createForm(this),
      username: 'liujie08080@163.com',
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
          title: 'IT/互联网',
          list: [
            {
              name: '电子商务'
            },
            {
              name: '互联网金融'
            },
            {
              name: '工具软件'
            },
            {
              name: '社交网络'
            }
          ]
        },
        {
          title: '房地产开发',
          list: [
            {
              name: '大型房地产集团'
            },
            {
              name: '房地产开发'
            },
            {
              name: '物业管理公司'
            },
            {
              name: '园林绿化'
            }
          ]
        },
        {
          title: '金融业',
          list: [
            {
              name: '银行业'
            },
            {
              name: '保险业'
            },
            {
              name: '证券公司'
            },
            {
              name: '会计师事务所'
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
          ['username', 'realname', 'phone', 'identifyCode', 'imageIdentifyCode'],
          { force: true },
          (err, values) => {
            if (!err) {
              // 将输入内容存入参数对象，并转换为 JSON字符串
              this.username = values.username
              let params = qs.stringify(values)
              console.log(params)
              // 设置 API url
              let url = this.url.adduser
              // 用户注册提示信息
              const hide = this.$message.loading('用户注册中，请稍后..', 0)
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
                    // 隐藏第一步、第三步，显示第二步：企业注册
                    this.step1 = false
                    this.step2 = true
                    this.step3 = false
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
          params = Object.assign(values, { teamManager: this.username })
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
                if(this.time>1){
                  this.startInter = setInterval(()=>{
                    
                    if(this.time>1){
                      this.time--
                    } else {
                      clearInterval(this.startInter)
                      console.log(111)
                      this.$router.push('/')
                    }
                  },1000)
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
