<template>
  <div class="register">
      <!-- 注册用户 -->
    <div class="register-one" v-if="step1">
      <div class="step">
        <img src="@/assets/img/register/step1.png">
      </div>
      <a-form 
        :form="form">
        <!-- 用户名 -->
        <a-form-item 
        v-bind="formItemLayout" 
        label="用户名">
          <a-input
            placeholder="请输入用户名"
            v-decorator="['username',{ rules:[{required: true, message: '请输入用户名'}]}]"
          ></a-input>
        </a-form-item>
        <a-form-item v-bind="formItemLayout" label="真实姓名">
          <a-input
            v-decorator="['realname',{ rules:[{required: true, message: '请输入真实姓名'}]}]"
            placeholder="请输入真实姓名"
          ></a-input>
        </a-form-item>
        <!-- 手机号 -->
        <a-form-item v-bind="formItemLayout" label="手机号码">
          <a-input
            v-decorator="['phone',{ rules:[{required: true, message: '请输入手机号码'}]}]"
            placeholder="请输入手机号码"
          ></a-input>
        </a-form-item>
        <!-- 图形验证码 -->
        <!-- <a-form-item v-bind="formItemLayout" label="图形验证码">
          <a-row :gutter="0">
            <a-col :span="14">
              <a-input class="code"></a-input>
            </a-col>
            <a-col :span="10">
              <j-graphic-code/>
            </a-col>
          </a-row>
        </a-form-item> -->
        <!-- 手机验证码 -->
        <a-form-item v-bind="formItemLayout" label="手机验证码">
          <a-row :gutter="0">
            <a-col :span="14">
              <a-input
                v-decorator="['identifyCode',{ rules:[{required: true, message: '请输入验证码'}]}]"
                placeholder="请输入验证码"
              ></a-input>
            </a-col>
            <a-col :span="10">
              <a-button @click="getCode(url.register)">获取验证码</a-button>
            </a-col>
          </a-row>
        </a-form-item>
        <a-form-item class="terms">
          <a-checkbox @change="getChecked">
            <span>我同意</span>&nbsp;
            <a href="/">服务条款</a>
          </a-checkbox>
        </a-form-item>
        <a-button type="primary" @click="toTwo(url.adduser)">下一步</a-button>
      </a-form>
      <span class="login">已有账号登录</span>
      <span class="help">帮助中心</span>
    </div>

    <!-- 创建团队 -->
    <div class="register-two" v-if="step2">
      <div class="step step2">
        <img src="@/assets/img/register/step2.png">
      </div>
      <a-form @submit="personalRegister">
        <a-form-item v-bind="formItemLayout" label="团队名称">
          <a-input
            v-decorator="['teamName',{ rules:[{required: true, message: '请输入团队名称'}]}]"
            placeholder="请输入团队名称"
          ></a-input>
        </a-form-item>
        <a-form-item v-bind="formItemLayout" label="行业类别">
          <a-row>
            <a-col :span="6">
              <a-select defaultValue="企业" @change="getTeamType">
                <a-select-option value="企业">企业</a-select-option>
                <a-select-option value="项目">项目</a-select-option>
              </a-select>
            </a-col>
            <a-col :span="18">
              <a-select></a-select>
            </a-col>
          </a-row>
        </a-form-item>
        <a-form-item v-bind="formItemLayout" label="团队规模">
          <a-select defaultValue="大型企业" @change="getTeamScale">
            <a-select-option value="大型企业">大型企业</a-select-option>
            <a-select-option value="中型企业">中型企业</a-select-option>
            <a-select-option value="小型企业">小型企业</a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item v-bind="formItemLayout" label="团队管理者">
          <a-input
            v-decorator="['teamManager',{ rules:[{required: true, message: '请输入团队管理者'}]}]"
            placeholder="请输入团队管理者"
          ></a-input>
        </a-form-item>
        <a-button type="primary" @click="toOne" class="left">上一步</a-button>
        <a-button type="primary" @click="toDone" class="right">下一步</a-button>
      </a-form>
      <span class="login">已有账号登录</span>
      <span class="help">帮助中心</span>
    </div>
    <!-- 注册完成 -->
    <div class="register-done" v-if="step3">
      <div class="step">
        <img src="@/assets/img/register/step3.png">
      </div>

      <div class="result">
        <img src="@/assets/img/register/registerdone.png">
        <p>恭喜您完成团队的创建</p>
      </div>
      <div class="jump">
        <p>系统将自动跳转{{time}}s</p>
        <a>{{teamName}}</a>
      </div>
      <div class="desc">如果系统没有自动跳转，请点击团队名进入</div>
    </div>

  </div>
</template>

<script>
import JGraphicCode from '@/components/cmp/JGraphicCode'
import { postAction } from '@/api/manage.js'

export default {
  name: 'UserRegister',
  components: {
    JGraphicCode
  },
  data() {
    return {
      form: this.$form.createForm(this),
      checked: '',
      teamType: '',
      teamScale: '',
      time: 3,
      teamName: 'lalalal',
      formItemLayout: {
        labelCol: {
          xs: { span: 24 },
          sm: { span: 7 }
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 10 }
        }
      },
      url: {
          login: "/sys/sendMessage/login",
          verify: "/sys/verifyMessageLogin",
          register: "/sys/sendMessage/register",
          adduser: "/sys/user/addUser"
        },
      step1: true,
      step2: false,
      step3: false
    }
  },
  methods: {
    getChecked(e){
      this.checked = e.target.checked
      console.log(this.checked)
    },
    getTeamType(type){
      this.teamType = type
      console.log(this.teamType)
    },
    getTeamScale(scale){
      this.teamScale = scale
      console.log(this.teamScale)
    },
    personalRegister() {
      console.log('待完成的方法!')
    },
    getCode(url) {
      let para = {
        phone: '15295510944',
        identifyCode: ''
      }
      postAction(url, para).then(res => {
        console.log(111)
        if (res.success) {
          console.log('成功了')
          console.log(res)
        }
      })

      /*  this.$emit('one2two'); */
    },
    toOne() {
      this.step1 = true
      this.step2 = false
      this.step3 = false
    },
    toTwo(url) {
      if(!this.checked){
        console.log("请仔细阅读服务条款，同意后方可注册")
      }else{
        this.form.validateFields((err,values) => {
          if(!err){
            console.log(values)
            let params = values
            postAction(url,params).then( (res)=>{
              console.log(222)
              if(res.success){
                console.log("用户注册成功，请填写企业信息")
                this.step1 = false
                this.step2 = true
                this.step3 = false
              }
            })
          }
        })
      }
      this.step1 = false
      this.step2 = true
      this.step3 = false
    },
    toDone() {
      this.step1 = false
      this.step2 = false
      this.step3 = true
    }
  }
}
</script>
<style lang="less" scoped>
  @import '~@/assets/less/userRegister.less';
</style>
