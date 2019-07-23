<template>
  <a-modal
    :footer="null"
    :title="null"
    :width="1000"
    :visible="visible"
    :confirmLoading="confirmLoading"
    :closable="false"
    cancelText="关闭"
    v-dialogDrag
    :maskClosable="false"
    style="top:5%;">
    <div class="title">
      <div>
        预定信息
      </div>
      <a-icon type="close" class="closeIcon" @click="handleCancel" />
    </div>

    <a-spin :spinning="confirmLoading">
      <a-form :form="form" style="margin-top: 25px">
        <a-row :gutter="24">
          <!--会议主题-->
          <a-col :md="12" :sm="8">
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="会议主题">
              <a-input
                :disabled="disabledValue"
                v-model="this.subject"/>
            </a-form-item>
          </a-col>
          <!--参与人数-->
          <a-col :md="12" :sm="8">
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="参与人数">
              <a-input
                :disabled="disabledValue"
                v-model="this.containNum"/>
            </a-form-item>
          </a-col>
          <a-col :md="12" :sm="8">
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="联系人/主持人">
              <a-input
                :disabled="disabledValue"
                v-model="this.reserveUserName"/>
            </a-form-item>
          </a-col>

          <a-col :md="12" :sm="8">
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="联系电话">
              <a-input
                :disabled="disabledValue"
                v-model="this.contactPhone"/>
            </a-form-item>
          </a-col>
          <!--会议室名称-->
          <a-col :md="12" :sm="8">
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="会议室名称">
              <a-input
                :disabled="disabledValue"
                v-model="this.roomName"/>
            </a-form-item>
          </a-col>
          <!--会议级别-->
          <a-col :md="12" :sm="8">
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="会议级别">
              <a-input
                :disabled="disabledValue"
                v-model="this.meetingLevel"/>
            </a-form-item>
          </a-col>
          <!--会议日期-->
          <a-col :md="12" :sm="8">
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="会议日期">
              <a-input
                :disabled="disabledValue"
                v-model="this.meetingDate"/>
            </a-form-item>
          </a-col>
          <a-col :md="12" :sm="8">
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="会议时间">

              <a-select style="width: 120px" :disabled="disabledValue" v-model='this.startTime'>
              </a-select>
              <span style="width: 20px;"> ~ </span>
              <a-select style="width: 120px" :disabled="disabledValue" v-model='this.endTime'>
              </a-select>

            </a-form-item>
          </a-col>
        </a-row>

        <!--院内主要参与者-->
        <a-row :gutter="24">
          <a-col :md="24" :sm="8">
            <a-form-item
              :labelCol="{
                xs: { span: 24},
                sm: { span: 3 },
                }"
              :wrapperCol="{
                xs: { span: 24},
                sm: { span: 20 },
                }"
              label="院内参与者">
              <!--<j-select-user-by-dep-->
              <!--v-decorator="['joinMember',{rules: [{ required: true, message: '请选择参与人' }],initialValue:this.loginUserName}]"/>-->
              <a-input
                :disabled="disabledValue"
                v-model="this.joinMember"/>
            </a-form-item>
          </a-col>
          <a-col :md="12" :sm="8">
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="预订人">
              <a-input
                :disabled="disabledValue"
                v-model="this.username"/>
            </a-form-item>
          </a-col>
          <a-col :md="12" :sm="8">
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="预定时间">
              <a-input
                :disabled="disabledValue"
                v-model="this.reserveTime"/>
            </a-form-item>
          </a-col>
        </a-row>
      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>
  import { httpAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import moment from "moment"
  import JDictSelectTag from '@/components/dict/JDictSelectTag'
  import JDate from '@/components/cmp/JDate'
  import qs from 'qs'

  export default {
    name: "MeetingRoomModal",
    components: {
      JDictSelectTag,
      JDate,
    },
    created() {

    },
    data () {
      return {
        disabledValue: true,
        visible: false,
        model: {},
        indexOne:'',
        indexTwo:'',
        endTimeDisabled:true,
        labelCol: {
          xs: { span: 24 },
          sm: { span: 6 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 },
        },
        confirmLoading: false,
        form: this.$form.createForm(this),
      //  定义数据
        subject:'',
        containNum:'',
        username:'',
        contactPhone:'',
        roomName:'',
        meetingLevel:'',
        meetingDate: '',
        startTime:'',
        endTime:'',
        joinMember:'',
        reserveUserName:'',
        reserveTime:'',
      }
    },
    methods: {
      add () {
        this.edit({});
      },
      edit (record) {
        this.form.resetFields();
        this.model = Object.assign({}, record);
        this.visible = true
        console.log(record,'dasdadadsads')
        // this.endCol = record.endCol;
        this.subject=record.subject
        this.containNum=record.containNum
        this.username=record.username
        this.contactPhone=record.contactPhone
        this.roomName=record.roomName
        this.meetingLevel=record.meetingLevel
        this.startTime=record.startTime
        this.endTime=record.endTime
        this.joinMember=record.joinMember
        this.reserveUserName=record.reserveUserName
        this.reserveTime=record.reserveTime
        this.meetingDate=record.meetingDate
        this.$nextTick(() => {
          setTimeout(()=>{
            this.form.setFieldsValue(
              pick(this.model,'subject','containNum','username','contactPhone','roomName','meetingLevel','startTime','endTime','joinMember','reserveUserName','reserveTime','meetingDate')
            )
          },0)
        });

      },
      close () {
        this.$emit('close');
        this.visible = false;
      },
      handleCancel () {
        this.close()
      },
    }
  }
</script>

<style lang="less" scoped>
  @import '~@assets/less/modal.less';
</style>