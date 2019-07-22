<template>
  <a-modal
    :footer="null"
    :title="null"
    :closable="false"
    :width="800"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleOk"
    @cancel="handleCancel"
    :okButtonProps="model.btns ? { style: 'display:none' } : {}"
    :cancelButtonProps="model.btns ? { style: 'display:none' } : {}"
    v-dialogDrag
    cancelText="关闭"
    :maskClosable="false"
    style="top:5%;"
  >
    

      <div class="title">
        <div>
          <img src="@assets/img/login/edit.png" />
          {{title}}
        </div>
        <a-icon type="close" class="closeIcon" @click="handleCancel" />
      </div>

    <a-spin :spinning="confirmLoading">
      <a-form :form="form" class="row">
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"

          label="姓名">
          <a-input  disabled placeholder="请输入姓名" v-decorator="['realname', {}]" />
        </a-form-item>
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
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="电子邮件">
          <a-input placeholder="请输入电子邮件" v-decorator="['email', validatorRules.email]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="手机号码">
          <a-input placeholder="请输入手机号码" v-decorator="['phone', validatorRules.phone]" />
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
        <!--<a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="小组名称">
          <a-input placeholder="请输入小组名称" v-decorator="['groupName', {}]" />
        </a-form-item>-->
        <a-form-item label="所属小组" :labelCol="labelCol"
                     :wrapperCol="wrapperCol">
          <a-select
            v-decorator="['groupName',{rules: [{ required: true, message: '请选择所属小组！' }]}]"
            placeholder="请选择所属小组">
            <a-select-option value="集团级平台">集团级平台</a-select-option>
            <a-select-option value="电网、水电">电网、水电</a-select-option>
            <a-select-option value="轨道交通">轨道交通</a-select-option>
            <a-select-option value="工程云研发">工程云研发</a-select-option>
            <a-select-option value="建筑">建筑</a-select-option>
            <a-select-option value="京津冀、华北区域">京津冀、华北区域</a-select-option>
            <a-select-option value="深圳、珠三角区域">深圳、珠三角区域</a-select-option>
            <a-select-option value="企业级平台">企业级平台</a-select-option>
            <a-select-option value="海外">海外</a-select-option>
            <a-select-option value="移民">移民</a-select-option>
            <a-select-option value="GIS研发">GIS研发</a-select-option>
            <a-select-option value="经营">经营</a-select-option>
            <a-select-option value="UI设计">UI设计</a-select-option>
            <a-select-option value="测试工作">测试工作</a-select-option>
            <a-select-option value="其他">其他</a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="办公号码">
          <a-input placeholder="请输入办公号码" v-decorator="['officePhone', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="兴趣爱好">
          <a-input placeholder="请输入兴趣爱好" v-decorator="['hobbys', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="个性签名">
          <a-textarea placeholder="请输入个性签名" v-decorator="['personalSignature', {}]" />
        </a-form-item>

      </a-form>
      <a-row :gutter="24"  style=" margin-left: 510px">
        <a-col :md="24" :sm="8" >
          <a-form-item class="btnClass">
            <a-button
              @click="handleCancel"
              icon="close"
              style="margin-right:10px"
              class="cancel"
            >关闭</a-button>
            <a-button
              @click="handleOk"
              icon="check"
              type="primary"
              style="margin-right:10px"
              class="confirm"
            >保存</a-button>
          </a-form-item>
        </a-col>
      </a-row>
    </a-spin>
  </a-modal>
</template>

<script>
  import { httpAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import Vue from 'vue'
  import { ACCESS_TOKEN } from "@/store/mutation-types"

  import ATextarea from "ant-design-vue/es/input/TextArea";

  export default {
    name: "SysUserCopyModal",
    components: {ATextarea},
    data () {
      return {
        title:"操作",
        visible: false,
        model: {

        },
        labelCol: {
          xs: { span: 24 },
          sm: { span: 4 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 20 },
        },
        labelCol1: {
          xs: { span: 24 },
          sm: { span: 4 },
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
          edit: "/sys/user/edit2",
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
          this.form.setFieldsValue(pick(this.model, 'realname', 'email', 'phone', 'groupName', 'officePhone','personalSignature','hobbys'));
		  //时间格式化
          /*this.form.setFieldsValue({birthday:this.model.birthday?moment(this.model.birthday):null})*/
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
  @import '~@assets/less/modal.less';

</style>