<template>
  <a-modal
    :title="title"
    :width="800"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleOk"
    @cancel="handleCancel"
    cancelText="取消">
    
    <a-spin :spinning="confirmLoading">
      <a-form :form="form">
      
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="请假人">
          <a-input placeholder="请输入发起人" v-decorator="['inputerFullname', {}]"/>
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="请假类型">
          <a-input placeholder="请输入任务类型" v-decorator="['type', {}]"/>
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="开始时间" >
          <a-date-picker v-decorator="['timeStart', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="结束时间">
          <a-date-picker v-decorator="['timeEnd', {}]"/>
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="请假原因">
          <a-input placeholder="请输入任务类型" v-decorator="['reason', {}]"/>
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="部门领导">
          <a-input placeholder="请输入任务名称" v-decorator="['departmentLeaderRealname', {}]"/>
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="部门领导">
          <a-input placeholder="请输入任务名称" v-decorator="['departmentLeaderUsername', {}]"/>
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="人事部门领导">
          <a-input placeholder="请输入环节名称" v-decorator="['hrLeaderRealname', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="人事部门领导">
          <a-input placeholder="请输入环节名称" v-decorator="['hrLeaderUsername', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="总经理">
          <a-input placeholder="请输入环节名称" v-decorator="['generalManagerRealname', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="总经理">
          <a-input placeholder="请输入环节名称" v-decorator="['generalManagerUsername', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="备注信息">
          <a-input placeholder="请输入执行人" v-decorator="['remarks', {}]" />
        </a-form-item>
        
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="审批意见">
          <a-textarea placeholder="请输入审批意见" v-decorator="['_taskComment', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
        >
          <a-radio-group @change="onChange" v-model="value" >
            <a-radio-button 
            :style="radioStyle" 
            v-for="item in btns"
            :key="item.taskId"
            :value="item.btnApi"
            >{{item.btnName}}</a-radio-button>
            <!-- <a-radio-button :style="radioStyle" :value="model.btns[0].btnApi">{{model.btns[0].btnName}}</a-radio-button>
            <a-radio-button :style="radioStyle" :value="model.btns[1].btnApi">{{model.btns[1].btnName}}</a-radio-button>
            <a-radio-button :style="radioStyle" :value="model.btns[2].btnApi">{{model.btns[2].btnName}}</a-radio-button>
            <a-radio-button :style="radioStyle" :value="model.btns[3].btnApi">{{model.btns[3].btnName}}</a-radio-button> -->
            
          </a-radio-group>
        </a-form-item>
      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>
  import { httpAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import moment from "moment"
  import { format } from 'path';
  import qs from 'qs'

  export default {
    name: "TaskModal",
    data () {
      return {
        value: '',
        taskId: '',
        radioStyle: '',
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
        },  
        url: {
          add: "/oa/leaveApplication/action",
          /* edit: "/oa/leaveApplication/edit", */
        },
      }
    },
    created () {
    },
    methods: {
      add () {
        this.edit({});
      },
      edit (record) {
        this.form.resetFields();
        console.log(record,'逗你玩的千万');
        this.model = Object.assign({},record.flowData.processVar, record.flowData, record.formData, {taskId: record.taskId});
        this.visible = true;
        this.taskId = record.taskId;
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,
          'inputerFullname',
          'type',
          'reason',
          'departmentLeaderRealname',
          'departmentLeaderUsername',
          'hrLeaderRealname',
          'hrLeaderUsername',
          'generalManagerRealname',
          'generalManagerUsername',
          'remarks',
          '_taskComment',
          ))
		  //时间格式化
          this.form.setFieldsValue({
            timeStart:this.model.timeStart?moment(this.model.timeStart):null,
            timeEnd:this.model.timeEnd?moment(this.model.timeEnd):null
          })
        });
      },
      close () {
        this.$emit('close');
        this.visible = false;
      },
      onChange(){
      },
      handleOk () {
        const that = this;
        // 触发表单验证
        this.form.validateFields((err, values) => {
          if (!err) {
            that.confirmLoading = true;
            let httpurl = ''
            let method = ''
            httpurl += this.url.add
            method = 'post'

           /*  if(!this.model.id){
              httpurl+=this.url.add;
              method = 'post';
            }else{
              httpurl+=this.url.edit;
               method = 'put';
            } */
            let formData = Object.assign(this.model, values, {api: this.value})
            //时间格式化
            /* formData.startTime = formData.startTime?formData.startTime.format('YYYY-MM-DD HH:mm:ss'):null; */
            formData = {
              api: formData.api,
              processDefinitionKey: formData.processDefinitionKey,
              processInstanceId: formData.processInstanceId,
              taskId: this.taskId,
              id: formData.id,
              _taskComment: formData._taskComment
            }
            formData = qs.stringify(formData)

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
    },
    computed: {
      rollback() {
        return this.model.rollBackAbleNodes
      },
      btns(){
        return this.model.btns
      }
    }
  }
</script>

<style lang="less" scoped>
  .radio-group{
    margin-left: 160px;}
</style>