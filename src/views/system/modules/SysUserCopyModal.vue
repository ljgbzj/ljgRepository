<template>
  <a-modal
    :title="title"
    :width="800"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleOk"
    @cancel="handleCancel"
    cancelText="关闭">
    
    <a-spin :spinning="confirmLoading">
      <a-form :form="form" class="row">
      
        <!--<a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="登录账号">
          <a-input placeholder="请输入登录账号" v-decorator = "[ 'username', validatorRules.username]"  />
        </a-form-item>-->
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="姓名">
          <a-input placeholder="请输入姓名" v-decorator="['realname', {}]" />
        </a-form-item>
        <!--<a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="密码">
          <a-input placeholder="请输入密码" v-decorator="['password', {}]" />
        </a-form-item>-->
        <!--<a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="md5密码盐">
          <a-input placeholder="请输入md5密码盐" v-decorator="['salt', {}]" />
        </a-form-item>-->
        <a-form-item label="头像" :labelCol="labelCol1" :wrapperCol="wrapperCol1" >
          <a-upload
            listType="picture-card"
            class="avatar-uploader"
            :showUploadList="false"
            :action="uploadAction"
            :data="{'isup':1}"
            :headers="headers"
            :beforeUpload="beforeUpload"
            @change="handleChange"
          >
            <img v-if="picUrl" :src="getAvatarView()" alt="头像" style="height:104px;max-width:300px"/>
            <div v-else>
              <a-icon :type="uploadLoading ? 'loading' : 'plus'" />
              <div class="ant-upload-text">上传</div>
            </div>
          </a-upload>
        </a-form-item>
       <!-- <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="生日">
          <a-date-picker showTime format='YYYY-MM-DD HH:mm:ss' v-decorator="[ 'birthday', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="性别（1：男 2：女）">
          <a-input-number v-decorator="[ 'sex', {}]" />
        </a-form-item>-->
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="电子邮件">
          <a-input placeholder="请输入电子邮件" v-decorator="['email', validatorRules.email]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="电话">
          <a-input placeholder="请输入电话" v-decorator="['phone', validatorRules.phone]" />
        </a-form-item>
       <!-- <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="部门code">
          <a-input placeholder="请输入部门code" v-decorator="['orgCode', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="状态(1：正常  2：冻结 ）">
          <a-input-number v-decorator="[ 'status', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="删除状态（0，正常，1已删除）">
          <a-input placeholder="请输入删除状态（0，正常，1已删除）" v-decorator="['delFlag', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="同步工作流引擎1同步0不同步">
          <a-input placeholder="请输入同步工作流引擎1同步0不同步" v-decorator="['activitiSync', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="所属租户">
          <a-input placeholder="请输入所属租户" v-decorator="['corpCode', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="所属项目">
          <a-input placeholder="请输入所属项目" v-decorator="['prjCode', {}]" />
        </a-form-item>-->
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="小组名称">
          <a-input placeholder="请输入小组名称" v-decorator="['groupName', {}]" />
        </a-form-item>
       <!-- <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="所属部门">
          <a-input placeholder="请输入所属部门" v-decorator="['departName', {}]" />
        </a-form-item>-->
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="办公号码">
          <a-input placeholder="请输入办公号码" v-decorator="['officePhone', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="办公地址">
          <a-input placeholder="请输入办公地址" v-decorator="['address', {}]" />
        </a-form-item>
		
      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>
  import { httpAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import Vue from 'vue'
  import moment from "moment"
  import { ACCESS_TOKEN } from "@/store/mutation-types"
  import {checkUsername} from "../../../api/api";

  export default {
    name: "SysUserCopyModal",
    data () {
      return {
        title:"操作",
        visible: false,
        model: {

        },
        labelCol: {
          xs: { span: 24 },
          sm: { span: 3 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 12 },
        },
        labelCol1: {
          xs: { span: 24 },
          sm: { span: 3 },
        },
        wrapperCol1: {
          xs: { span: 24 },
          sm: { span: 4 },
        },
        uploadLoading:false,
        confirmLoading: false,
        form: this.$form.createForm(this),
        validatorRules:{
          phone:{rules: [{validator: this.validatePhone}]},
          email:{rules: [{type: 'email', message: '请输入正确格式的电子邮箱!',}]},
          /*username:{rules: [{required:true,message: '请输入用户账号'}, {validator: this.validateUsername}]}*/
          //  sex:{initialValue:((!this.model.sex)?"": (this.model.sex+""))}
        },
        headers:{},
        picUrl: '',
        url: {
          // add: "/test/sysUserCopy/add",
          // edit: "/test/sysUserCopy/edit",
          add: "/sys/user/add",
          edit: "/sys/user/edit",
          userId:"/sys/user/generateUserId", // 引入生成添加用户情况下的url
          fileUpload: window._CONFIG['domianURL']+"/sys/common/upload",
          imgerver: window._CONFIG['domianURL']+"/sys/common/view",
        },
      }
    },
    created () {
      const token = Vue.ls.get(ACCESS_TOKEN);
      this.headers = {"X-Access-Token":token}
    },
    computed:{
      uploadAction:function () {
        return this.url.fileUpload;
      }
    },
    methods: {
      add () {
        this.edit({});
      },
      edit (record) {
        this.form.resetFields();
        this.model = Object.assign({}, record);
        this.visible = true;
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,'username','realname', 'email','phone', 'groupName', 'officePhone','address'));
		  //时间格式化
          this.form.setFieldsValue({birthday:this.model.birthday?moment(this.model.birthday):null})
        });
        if (record.avatar) {
          this.picUrl = true;
        }
      },
      close () {
        this.$emit('close');
        this.visible = false;
        this.picUrl = '';
      },
      handleOk () {
        const that = this;
        // 触发表单验证
        this.form.validateFields((err, values) => {
          if (!err) {
            that.confirmLoading = true;
            let httpurl = '';
            let method = '';
            if(!this.model.id){
              httpurl+=this.url.add;
              method = 'post';
            }else{
              httpurl+=this.url.edit;
               method = 'post'; // put修改
            }
            let formData = Object.assign(this.model, values);
            //时间格式化
            formData.birthday = formData.birthday?formData.birthday.format('YYYY-MM-DD HH:mm:ss'):null;
            
            console.log(formData)
            httpAction(httpurl,formData,method).then((res)=>{
              if(res.success){
                that.$message.success(res.message);
                that.$emit('ok');
              }else{
                that.$message.warning(res.message);
              }
            }).finally(() => {
              that.confirmLoading = false;
              that.close();
            })
          }
        })
      },
      handleCancel () {
        this.close()
      },


      validatePhone(rule, value, callback){
        if(!value || new RegExp(/^1[3|4|5|6|7|8][0-9]\d{8}$/).test(value)){
          callback();
        }else{
          callback("请输入正确格式的手机号码!");
        }
      },
      /*validateUsername(rule, value, callback){
        var params = {
          id:this.model.id,
          username:value
        };
        checkUsername(params).then((res)=>{
          if(res.success){
            callback();
          }else{
            callback("用户账号已存在！");
          }
        });
      },*/
      /*图片上传之前的操作*/
      beforeUpload: function(file){
        var fileType = file.type;
        if(fileType.indexOf('image')<0){
          this.$message.warning('请上传图片');
          return false;
        }
        //TODO 验证文件大小
      },
      handleChange (info) {
        this.picUrl = "";
        if (info.file.status === 'uploading') {
          this.uploadLoading = true;
          return
        }
        if (info.file.status === 'done') {
          var response = info.file.response;
          this.uploadLoading = false;
          console.log(response);
          if(response.success){
            this.model.avatar = response.message;
            this.picUrl = "Has no pic url yet";
          }else{
            this.$message.warning(response.message);
          }
        }
      },
      getAvatarView(record){
        if (record == undefined) {
          return this.url.imgerver +"/"+ this.model.avatar;
        } else {
          return this.url.imgerver +"/"+ record.avatar;
        }

      },
    }
  }
</script>

<style lang="less" scoped>

</style>