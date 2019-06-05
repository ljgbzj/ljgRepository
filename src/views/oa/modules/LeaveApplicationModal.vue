<template>
  <a-modal
    :title="title"
    :width="800"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleOk"
    @cancel="handleCancel"
    cancelText="关闭">
    <a-tabs defaultActiveKey="1">
      <a-tab-pane key="1">
        <span slot="tab">
          <a-icon type="project" />
          发起表单
        </span>
        <a-spin :spinning="confirmLoading">
          <a-form :form="form">        
            <!-- <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="请假人用户编码">
              <a-input placeholder="请输入请假人用户编码" v-decorator="['username', {}]" />
            </a-form-item> -->
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="请假人">
              <a-input placeholder="请输入请假人姓名" v-decorator="['inputerFullname', validatorRules.templateName]" disabled/>
            </a-form-item>
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="请假类型">
              <!-- <a-input placeholder="请输入请假类型" v-decorator="['type', {}]" /> -->
              <a-select placeholder="请选择类型" v-decorator="['type', validatorRules.templateType]">
                <a-select-option value="0">事假</a-select-option>
                <a-select-option value="1">病假</a-select-option>
                <a-select-option value="2">年假</a-select-option>
                <a-select-option value="3">婚假</a-select-option>
                <a-select-option value="4">出差</a-select-option>
              </a-select>
            </a-form-item>
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="请假开始时间">
              <a-date-picker showTime format='YYYY-MM-DD HH:mm:ss' v-decorator="[ 'timeStart', validatorRules.templateStartT]" />
            </a-form-item>
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="请假结束时间">
              <a-date-picker showTime format='YYYY-MM-DD HH:mm:ss' v-decorator="[ 'timeEnd', validatorRules.templateEndT]" />
            </a-form-item>
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="请假原因">
              <a-input placeholder="请输入请假原因" v-decorator="['reason', validatorRules.templateReason]" />
            </a-form-item>
            <!--  人员选择控件 -->
            <!-- <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="部门领导">
              <j-select-user-by-dep v-decorator="['departmentLeaderUsername']"></j-select-user-by-dep>
            </a-form-item> -->
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="部门领导">
              <a-input placeholder="请输入部门领导" v-decorator="['departmentLeaderUsername', {}]" />
            </a-form-item>
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="部门领导">
              <a-input placeholder="请输入部门领导" v-decorator="['departmentLeaderRealname', {}]" />
            </a-form-item>
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="人事部门领导">
              <a-input placeholder="请输入人事部门领导" v-decorator="['hrLeaderUsername', {}]" />
            </a-form-item>
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="人事部门领导">
              <a-input placeholder="请输入人事部门领导" v-decorator="['hrLeaderRealname', {}]" />
            </a-form-item>
            <!-- <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="人事部门领导">
              <j-select-user-by-dep v-decorator="['hrLeaderUsername']"></j-select-user-by-dep>
            </a-form-item>
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="总经理">
              <j-select-user-by-dep v-decorator="['generalManagerUsername']"></j-select-user-by-dep>
            </a-form-item> -->
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="总经理">
              <a-input placeholder="请输入总经理" v-decorator="['generalManagerUsername', {}]" />
            </a-form-item>
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="总经理">
              <a-input placeholder="请输入总经理" v-decorator="['generalManagerRealname', {}]" />
            </a-form-item>
            <!-- <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="流程定义ID">
              <a-input placeholder="请输入流程定义ID" v-decorator="['processDefinitionId', {}]" />
            </a-form-item> -->
            <!-- <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="流程实例ID">
              <a-input placeholder="请输入流程实例ID" v-decorator="['processInstanceId', {}]" />
            </a-form-item> -->
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="备注信息">
              <a-input placeholder="请输入备注信息" v-decorator="['remarks', validatorRules.templateContent]" />
            </a-form-item>
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="状态">
              <a-input-number v-decorator="[ 'status', {}]" />
            </a-form-item>
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="评论留言">
              <a-textarea placeholder="评论意见" :rows="4" v-decorator="[ 'content', {}]"/>
            </a-form-item>
            <!-- <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="所属租户">
              <a-input placeholder="请输入所属租户" v-decorator="['corpCode', {}]" />
            </a-form-item> -->
        
          </a-form>
        </a-spin>
      </a-tab-pane>
      <a-tab-pane key="2" forceRender>
        <span slot="tab">
          <a-icon type="bell" />
          流程图
        </span>
        这是流程图
      </a-tab-pane>
    </a-tabs>
  </a-modal>
</template>

<script>
  import { httpAction, httpActionHeader } from '@/api/manage'
  import pick from 'lodash.pick'
  import moment from "moment"
  import JSelectUserByDep from '@/components/cmpbiz/JSelectUserByDep'
  import { mapGetters } from 'vuex'
  import qs from 'qs';

import { setTimeout } from 'timers';

  export default {
    name: "LeaveApplicationModal",
    components: {
      JSelectUserByDep
    },
    data () {
      return {
        title:"操作",
        visible: false,
        model: {},
        labelCol: {
          xs: { span: 24 },
          sm: { span: 5 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 },
        },

        confirmLoading: false,
        form: this.$form.createForm(this),
        validatorRules:{
          templateName: {rules: [{required: true, message: '请输入请假人姓名!'}]},
          templateType: {rules: [{required: true, message: '请选择请假类型!'}]},
          templateStartT: {rules: [{required: true, message: '请选择开始时间!'}]},
          templateEndT: {rules: [{required: true, message: '请选择开结束时间!'}]},
          templateReason: {rules: [{required: true, message: '请输入请假原因!'}]},
          templateDeLeader: {rules: [{required: true, message: '请选择部门领导!'}]},
          templatehrLeader: {rules: [{required: true, message: '请选择人事部门领导!'}]},
          templateGeManager: {rules: [{required: true, message: '请选择总经理!'}]},
          templateContent: {rules: []},
        },
        url: {
          add: "/oa/leaveApplication/action",
          edit: "/oa/leaveApplication/edit",
        },
      }
    },
    created () {
    },
    methods: {
      ...mapGetters(["nickname","userInfo"]),
      add () {
        this.edit({});
      },
      edit (record) {
        this.form.resetFields();
        this.model.inputerFullname = this.nickname();
        this.model = Object.assign({}, record);
        this.visible = true;

        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,'inputerFullname','type','reason','departmentLeaderUsername','departmentLeaderRealname','hrLeaderUsername','hrLeaderRealname','generalManagerUsername','generalManagerRealname','processDefinitionId','processInstanceId','remarks'))
		  //时间格式化
          this.form.setFieldsValue({timeStart:this.model.timeStart?moment(this.model.timeStart):null})
          this.form.setFieldsValue({timeEnd:this.model.timeEnd?moment(this.model.timeEnd):null})
          this.form.setFieldsValue({inputerFullname: this.nickname()})
        });

      },
      close () {
        this.$emit('close');
        this.visible = false;
      },
      handleOk () {
        const that = this;
        // 触发表单验证
        this.form.validateFields((err, values) => {
          console.log();
          if (!err) {
            that.confirmLoading = true;
            let httpurl = '';
            let method = '';
            let qsMothods = '';
            let formData = Object.assign(this.model, values);
            if(!this.model.id){
              httpurl+=this.url.add;
              method = 'post';
              formData.api = '/process/startAndSubmit';
              formData.processDefinitionKey = 'leave';
            }else{
              httpurl+=this.url.edit;
               method = 'put';
            }
            // let formData = Object.assign(this.model, values);

            //时间格式化
            formData.timeStart = formData.timeStart?formData.timeStart.format('YYYY-MM-DD HH:mm:ss'):null;
            formData.timeEnd = formData.timeEnd?formData.timeEnd.format('YYYY-MM-DD HH:mm:ss'):null;
            
            console.log(formData);
            if(method == 'post'){
              httpAction(httpurl,qs.stringify(formData),method).then((res)=>{
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
            } else {
              httpActionHeader(httpurl,JSON.stringify(formData), method).then((res)=>{
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
          }
        })
      },
      handleCancel () {
        this.close()
      },


    }
  }
</script>

<style lang="less" scoped>

</style>