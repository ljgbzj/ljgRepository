<template>
  <!--<a-modal-->
  <!--:title="title"-->
  <!--:footer="null"-->
  <!--:width="1000"-->
  <!--:visible="visible"-->
  <!--:confirmLoading="confirmLoading"-->
  <!--@ok="handleOk"-->
  <!--@cancel="handleCancel"-->
  <!--cancelText="关闭"-->
  <!--:maskClosable="false"-->
  <!--style="top:5%;">-->
  <a-modal
    title="会议室预定"
    :width="1000"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleOk"
    @cancel="handleCancel"
    style="top:5%;">

    <a-spin :spinning="confirmLoading">
      <a-form :form="form">
        <a-row :gutter="24">

          <!--会议主题-->
          <a-col :md="12" :sm="8">
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="会议主题">
              <a-input placeholder="请输入会议主题" v-decorator="['subject', validatorRules.subject]" />
            </a-form-item>
          </a-col>
          <!--参与人数-->
          <a-col :md="12" :sm="8">
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="参与人数">
              <a-input
                style="width: 50%"
                placeholder="请输入参与人数"
                v-decorator="[ 'memberNumber', validatorRules.memberNumber]"
              />
              最大容纳人数：{{this.containNum}}
            </a-form-item>
          </a-col>
          <a-col :md="12" :sm="8">
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="联系人/主持人">
              <j-select-user-new
                :selectedDetails="auditUsers1"
                @callback="setAuditUser"
                class="userSelect"></j-select-user-new>
            </a-form-item>
          </a-col>

          <a-col :md="12" :sm="8">
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="联系电话">
              <a-input
                v-decorator="[ 'contactPhone', validatorRules.contactPhone]"/>
            </a-form-item>
          </a-col>
          <!--会议室名称-->
          <a-col :md="12" :sm="8">
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="会议室名称">
              <!--<a-input placeholder="请输入会议室名" v-decorator="['meetingRoom', validatorRules.meetingRoom]"/>-->
              <a-select v-decorator="['meetingRoom', {}]">
                <a-select-option v-for="(item,index) in meetingRoomList" :key="item.roomName" @click="selectMeetingRoomName(item,index)" >{{item.roomName}}</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <!--会议级别-->
          <a-col :md="12" :sm="8">
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="会议类型">
              <j-dict-select-tag
                v-decorator="['meetingLevel', {initialValue:'小组例会'}]"
                :triggerChange="true"
                placeholder="请选择会议类型"
                dictCode="meeting_level"
              />
            </a-form-item>
          </a-col>
          <a-col :md="12" :sm="8">
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="会议日期">
              <j-date placeholder="请选择会议日期" :triggerChange="true" v-decorator="['meetingDate', validatorRules.meetingDate]" dateFormat="YYYY-MM-DD"/>
            </a-form-item>
          </a-col>
          <a-col :md="12" :sm="8">
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="会议时间">

              <a-select style="width: 120px" v-model='model.meetingStartTime'>
                <a-select-option v-for="(time1,index) in timeNode" :key="time1" @click="selectChat1(time1,index)" >{{time1}}</a-select-option>
              </a-select>
              <span style="width: 20px;"> ~ </span>
              <a-select style="width: 120px" :disabled="endTimeDisabled" v-model='model.meetingEndTime'>
                <a-select-option v-for="(time2,index) in timeNode_copy" :key="time2" @click="selectChat2(time2,index)">{{time2}}</a-select-option>
              </a-select>

            </a-form-item>
          </a-col>
          <!--<a-col :md="12" :sm="8" style="display: none">-->
          <!--<a-form-item-->
          <!--:labelCol="labelCol"-->
          <!--:wrapperCol="wrapperCol"-->
          <!--label="申请部门">-->
          <!--<j-select-depart-->
          <!--v-decorator="['applyDepart',{ }]"-->
          <!--:trigger-change="true" ></j-select-depart>-->
          <!--</a-form-item>-->
          <!--</a-col>-->
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
              <j-select-user-new :selectedDetails="auditUsers2" @callback="setAuditUser" class="userSelect"></j-select-user-new>
            </a-form-item>
          </a-col>
          <a-col :md="12" :sm="8">
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="预订人">
              <a-input
                :disabled="disabledValue"
                v-decorator="['contact',{ rules: [{ required: true, message: '预订人' }]}]"/>
            </a-form-item>
          </a-col>
          <a-col :md="12" :sm="8">
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="预定时间">
              <a-input
                :disabled="disabledValue"
                v-decorator="['reserveDate',{initialValue:moment().format('YYYY-MM-DD HH:mm:ss')}]"/>
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
  import JSelectUserNew from '@/components/cmpbiz/JSelectUserNew'
  import { CmpListMixin } from '@/mixins/CmpListMixin'

  const timeNode = [
    "8:30",
    "9:00",
    "9:30",
    "10:00",
    "10:30",
    "11:00",
    "11:30",
    "12:00",
    "12:30",
    "13:00",
    "13:30",
    "14:00",
    "14:30",
    "15:00",
    "15:30",
    "16:00",
    "16:30",
    "17:00",
    "17:30"];
  export default {
    name: "MeetingRoomModal",
    components: {
      JSelectUserNew,
      JDictSelectTag,
      JDate,
    },
    mixins: [CmpListMixin],
    created() {
      const that = this
      that.timeNode_copy = timeNode.slice();
      that.axios.get('/meetingRoom/meetingRoomList/allList')
        .then(function(response) {
          that.meetingRoomList = response
          console.log(that.meetingRoomList)
        })
    },
    data () {
      return {
        disabledValue:true,
        meetingRoomList:'',
        meetingRoomId:'',
        startTime:'',
        endTime:'',
        startCol:'',
        endCol:'',
        title:"操作",
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
        timeNode,
        timeNode_copy:[],
        confirmLoading: false,
        form: this.$form.createForm(this),
        validatorRules:{
          meetingRoom:{rules: [{ required: true, message: '请输入会议室名!' }]},
          subject:{rules: [{ required: true, message: '请输入会议主题!' }]},
          contact:{rules: [{ required: true, message: '请输入联系人!' }]},
          contactPhone:{rules: [{validator: this.validatePhone},{ required: true, message: '请输入手机号码' }],initialValue:this.$store.getters.userInfo.phone},
          meetingDate:{rules: [{ required: true, message: '请输入会议日期!' }]},
          memberNumber:{ rules: [{ required: true, message: '参与人数' },{validator: this.memberNumbercheck}]}
        },
        url: {
          list: "/meetingRoom/meetingRoomList/allList",
          edit: "/meetingRoom/edit",
        },
        // 最大容纳数
        containNum:'',
        reserveUserName:'',
        reserveFullName:'',
        joinMemberUserName:'',
        joinMemberFullName:'',
        selectUser: ['auditUsers1', 'auditUsers2'],
        auditUsers1: {
          colum: 'auditUsers1',
          value: [],
          target: [
            { to: 'reserveUserName', from: 'username' },
            { to: 'reserveFullName', from: 'realname' }
          ]
        },
        auditUsers2: {
          colum: 'auditUsers2',
          value: [],
          target: [
            { to: 'joinMemberUserName', from: 'username' },
            { to: 'joinMemberFullName', from: 'realname' }
          ]
        },
      }
    },
    methods: {
      moment,
      recordIndex(value) {
        var indexOne = this.timeNode.indexOf(value)
      },
      check(value) {
        var indexTwo = this.timeNode.indexOf(value)
        console.log(indexTwo);
        if((this.indexTwo - this.indexOne) <= 0){
          //this.$message.warning("结束时间不能小于等于开始时间");
          // alert(this.time2);
        }
      },

      add () {
        this.edit({});
      },
      edit (record) {
        const that = this
        that.axios.get('/meetingRoom/meetingRoomList/findById?roomId=' + record.meetingRoomId)
          .then(function (response){
            that.containNum = response.containNum
          })
        // 初始化选人组件字段
        that.form.resetFields();
        that.model = Object.assign({}, record);
        that.visible = true;
        that.meetingRoomId = record.meetingRoomId;
        that.startCol = record.startCol;
        that.endCol = record.endCol;

        that.$nextTick(() => {
          setTimeout(()=>{
            that.form.setFieldsValue(
              pick(that.model,
                'meetingRoom',
                'subject',
                'contact',
                'contactPhone',
                'meetingDate',
                'meetingStartTime',
                'meetingEndTime',
                'joinMemberFullName',
                'joinMemberUserName',
                'memberNumber',
                'reserveDate',
                'reserveFullName',
                'reserveUserName',
                'reserveFullName',
                'reserveUserName'
              )
            )
          },0)
          // 初始化选人组件字段
          this.auditUsers1.value = []
          this.auditUsers2.value = []
          // 初始化选人控件
          this.initSelectMan(this,record);
          // this.form.setFieldsValue(pick(this.model,'meetingRoomId','meetingRoom','subject','applyDepart','departLeader','contactId','contact','contactPhone','meetingDate','meetingStartTime','meetingEndTime','memberNumber','meetingLevel','reserveUserId','reserveUser','status','startCol','endCol'))
          //时间格式化
          // this.form.setFieldsValue({reserveDate:this.model.reserveDate?moment(this.model.reserveDate):null})
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
          if (!err) {
            that.confirmLoading = true;
            let httpurl = '';
            let method = 'post';
            if(!this.model.id){
              httpurl+=this.url.edit;
            }else{
              httpurl+=this.url.edit;
            }
            values.startCol = this.startCol;
            values.endCol = this.endCol;
            values.meetingRoomId = this.meetingRoomId;
            let formData = Object.assign(this.model, values);

            that.axios.post('/meetingRoom/meetingRoomTimeStatus/edit',formData)
              .then(function (response){
                if (response.code === 200){
                  console.log(response)
                  httpAction(httpurl,qs.stringify(formData),method).then((res)=>{
                    if(res.success){
                      that.$message.success(res.message);
                      that.$emit('ok');
                    }else{
                      that.$message.warning(res.message);
                    }
                  })
                }else {
                  that.$message.warning(response.message);
                  that.confirmLoading = false
                }
              })
              .finally(() => {
                that.confirmLoading = false;
                that.close();
              })
          }
        })
      },
      handleCancel () {
        this.close()
      },
      selectChat1(time1,index){
        this.startTime = time1;
        this.startCol = index
        this.timeNode_copy.splice(0, index+1);
        this.endTimeDisabled = false;
        console.log(this.startCol,"开始的序号")
      },
      selectChat2(time2,index){
        this.endTime = time2
        this.endCol = index + this.startCol
        this.timeNode_copy = timeNode.slice();
        this.endTimeDisabled = true;
        console.log(this.endCol,"结束的序号")
      },
      selectMeetingRoomName(item){
        this.meetingRoom = item.roomName
        this.meetingRoomId = item.id
      },
      memberNumbercheck(rule, value, callback){
        const r = /^\+?[1-9][0-9]*$/;
        if (r.test(value) && value <= this.containNum){
          callback();
        } else if (r.test(value) && value > this.containNum){
          callback("人数超过了会议室最大容纳人数");
        } else {
          callback("请输入数字!");
        }
      },
      validatePhone(rule, value, callback){
        if(!value || new RegExp(/^1[3|4|5|7|8][0-9]\d{8}$/).test(value)){
          callback();
        }else{
          callback("请输入正确格式的手机号码!");
        }
      },

    }
  }
</script>

<style lang="less" scoped>
  @import '~@assets/less/modal.less';
</style>