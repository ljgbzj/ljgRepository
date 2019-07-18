<template>
  <div>
    <a-card title="会议室预定" :bordered="false" v-model="hoverRoomName">
      <!-- 查询区域 -->
      <div class="table-page-search-wrapper">
        <a-form layout="inline">
          <a-row :gutter="24">
            <a-col :md="6" :sm="8">
              <a-form-item label="日期">
                <a-date-picker
                  :disabledDate="disabledDate"
                  :defaultValue="moment(moment(),'YYYY-MM-DD')"
                  v-model="queryParam.searchDate"
                  @change="onChange" />
              </a-form-item>
            </a-col>
            <a-col :md="6" :sm="8">
              <a-form-item label="会议室">
                <a-input placeholder="请输入会议室名称" v-decorator="['searchRoomName',{}]"></a-input>
              </a-form-item>
            </a-col>
            <a-col :md="3" :sm="8">
              <a-form-item label="选择会议室">
              <a-input placeholder="会议室名称" v-model="this.orderRoom" :disabled="disabledValue"></a-input>
              </a-form-item>
            </a-col>
            <a-col :md="3" :sm="8">
              <a-form-item label="开会时间">
                <a-input placeholder="开会时间段" v-model="this.orderTime" :disabled="disabledValue"></a-input>
              </a-form-item>
            </a-col>
            <a-col :md="6" :sm="8" >
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
              <a-button type="primary" @click="order" icon="check" style="margin-left: 8px">预定</a-button>
            </span>
            </a-col>
          </a-row>
        </a-form>
      </div>
      <!--表头-->
      <a-spin :spinning="confirmLoading2" class="loading">
      <div class="father-box">
        <div class="title-room-name-box">
          <div class="title-box">会议室名称</div>
        </div>
        <div class="title-contain-number">
          <div class="title-box">人数</div>
        </div>
        <div class="room-time-father-box">
          <div class="title-room-time-box" v-for="(timeList, index) in timeList">
            <div class="title-box">{{timeList.time}}</div>
          </div>
        </div>
      </div>
      <!-- 对会议室清单进行遍历 -->

      <div v-for="(item, index1) in roomList" class="father-box" >
        <div class="content-room-name-box">
          <div class="content-box" :hoverable="true">{{item.roomName}}</div>
        </div>
        <div class="content-contain-number">
          <div class="content-box" :hoverable="true">{{item.containNum}}</div>
        </div>
        <div class="room-time-father-box">
          <!-- 对会议室的时间点状态进行遍历 -->
          <div
            v-for="(items,index) in item.arrUseInfo "
            class="content-room-time-box"
            @mousedown="myMouseDown(item, items.status,index ,index1)"
            @mouseup="myMouseUp(item, items.status,index, index1)"
            @mouseover="mymouseOver(item, items.status,index, index1)"
            :class="{ mousedown:items.status == 2}"
          >
            <!--被预约,自己预定的-->
            <div
              class="content-box"
              v-bind:class="{ active: true }"
              v-if="items.username != null && items.length != 0 && items.subject != null && items.userId == loginUserId"
              :style="{width: getWidth(items.length)}"
              @mouseenter="enter(item, items.status,index)"
              @mouseleave="leave"
            >
                <div class="info-icon-order">
                  {{items.username}}
                </div>
            </div>
            <!--被预约,别人预定的-->
            <div
              class="content-box"
              v-bind:class="{ activeOthers: true }"
              v-if="items.username != null && items.length != 0 && items.subject != null && items.userId != loginUserId"
              :style="{width: getWidth(items.length)}"
              @mouseenter="enter(item, items.status,index)"
              @mouseleave="leave"
            >
              <div class="info-icon-order">
                {{items.username}}
              </div>
            </div>
            <!--隐藏单元格-->
            <div
              v-else-if="items.username != null && items.length == 0"
              :style="{display: true}"
              @mouseenter="enter(item, items.status,index)"
              @mouseleave="leave"
            >
            </div>
            <!--如果没有预约-->
            <div
              class="content-box"
              v-else-if="items.usernameusername == null && items.status == 0"
              @mouseenter="enter(item, items.status,index)"
              @mouseleave="leave"
            >
              <img src="@/assets/img/meeting/free.png" draggable="false"></img>
            </div>
            <div
              class="content-box"
              v-else-if="items.usernameusername == null && items.status == 2"
              @mouseenter="enter(item, items.status,index)"
              @mouseleave="leave"
            >
              <img src="@/assets/img/meeting/reserved.png" draggable="false"></img>
            </div>
          </div>
        </div>
        <!-- </a-row> -->
      </div>
      </a-spin>
    </a-card>
    <!--表单区域-->
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

            <a-col :md="12" :sm="8">
              <a-form-item
                :labelCol="labelCol"
                :wrapperCol="wrapperCol"
                label="会议主题">
                <a-input
                  placeholder="请输入会议主题"
                  v-decorator="['subject', { rules: [{ required: true, message: '请输入会议主题' }]}]" />
              </a-form-item>
            </a-col>
            <a-col :md="12" :sm="8">
              <a-form-item
                :labelCol="labelCol"
                :wrapperCol="wrapperCol"
                label="参与人数">
                <a-input
                  placeholder="请输入参与人数"
                  v-decorator="[ 'memberNumber', { rules: [{ required: true, message: '参与人数' }]}]" />
              </a-form-item>
            </a-col>

            <a-col :md="12" :sm="8" style="display: none">
              <a-form-item
                :labelCol="labelCol"
                :wrapperCol="wrapperCol"
                label="申请部门">
                <j-select-depart
                  v-decorator="['applyDepart',{ }]"
                  :trigger-change="true" ></j-select-depart>
              </a-form-item>
            </a-col>

            <a-col :md="12" :sm="8" style="display: none">
              <a-form-item
                :labelCol="labelCol"
                :wrapperCol="wrapperCol"
                label="部门领导">
                <j-select-user-by-dep
                  v-decorator="['departLeader',{}]"></j-select-user-by-dep>
              </a-form-item>
            </a-col>

            <a-col :md="12" :sm="8">
              <a-form-item
                :labelCol="labelCol"
                :wrapperCol="wrapperCol"
                label="联系人">
                <a-input
                  :disabled="disabledValue"
                  v-decorator="['contact',{initialValue:this.loginUserName}]"/>
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

            <a-col :md="12" :sm="8">
              <a-form-item
                :labelCol="labelCol"
                :wrapperCol="wrapperCol"
                label="开始时间">
                <a-input
                  :disabled="disabledValue"
                  v-decorator="['meetingStartTime',{initialValue:this.startTime}]"/>
              </a-form-item>
            </a-col>
            <a-col :md="12" :sm="8">
              <a-form-item
                :labelCol="labelCol"
                :wrapperCol="wrapperCol"
                label="结束时间">
                <a-input
                  :disabled="disabledValue"
                  v-decorator="['meetingEndTime',{initialValue:this.endTime}]"/>
              </a-form-item>
            </a-col>

            <a-col :md="12" :sm="8">
              <a-form-item
                :labelCol="labelCol"
                :wrapperCol="wrapperCol"
                label="预定人">
                <!--<j-select-user-by-dep-->
                  <!--v-decorator="['reserveUser',{rules: [{ required: true, message: '请选择预订人' }],initialValue:this.loginUserName}]"/>-->
                <j-select-user-new :selectedDetails="auditUsers1" @callback="setAuditUser" class="userSelect"></j-select-user-new>
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

            <a-col :md="12" :sm="8">
              <a-form-item
                :labelCol="labelCol"
                :wrapperCol="wrapperCol"
                label="会议室名称">
                <a-input
                  :disabled="disabledValue"
                  v-decorator="['meetingRoom',{initialValue:this.orderRoom}]"/>
              </a-form-item>
            </a-col>

            <a-col :md="12" :sm="8">
              <a-form-item
                :labelCol="labelCol"
                :wrapperCol="wrapperCol"
                label="会议日期">
                <a-date-picker
                  :disabledDate="disabledDate"
                  v-decorator="['meetingDate',{initialValue:this.queryParam.searchDate}]"
                  @change="onChange" />
              </a-form-item>
            </a-col>

            <a-col :md="12" :sm="8">
              <a-form-item
                :labelCol="labelCol"
                :wrapperCol="wrapperCol"
                label="会议级别">
                <j-dict-select-tag
                  v-decorator="['meetingLevel', {initialValue:'例会'}]"
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
                label="院内参与者">
                <!--<j-select-user-by-dep-->
                  <!--v-decorator="['joinMember',{rules: [{ required: true, message: '请选择参与人' }],initialValue:this.loginUserName}]"/>-->
                <j-select-user-new :selectedDetails="auditUsers2" @callback="setAuditUser" class="userSelect"></j-select-user-new>
              </a-form-item>
            </a-col>

            <a-form-item label="meetingRoomId" style="display: none">
              <input v-decorator="['meetingRoomId',{initialValue:this.orderRoomId}]"/>
            </a-form-item>
          </a-row>

        </a-form>
      </a-spin>
    </a-modal>
  </div>
</template>

<script>
  import moment from 'moment';
  import JSelectDepart from '../../components/cmpbiz/JSelectDepart'
  import JSelectUserByDep from '../../components/cmpbiz/JSelectUserByDep'
  import { CmpListMixin } from '@/mixins/CmpListMixin'
  import { mapGetters } from 'vuex'
  import ARow from 'ant-design-vue/es/grid/Row'
  import ACol from 'ant-design-vue/es/grid/Col'
  import AFormItem from 'ant-design-vue/es/form/FormItem'
  import {findRoomList} from '../../api/api'
  import ARadioGroup from 'ant-design-vue/es/radio/Group'
  import ARadioButton from 'ant-design-vue/es/radio/RadioButton'
  import JDictSelectTag from '@/components/dict/JDictSelectTag'
  import JSelectUserNew from '../../components/cmpbiz/JSelectUserNew'


  export default {
    name: 'roomListOrder',
    components: { JSelectUserNew, ARadioButton, ARadioGroup, AFormItem, ACol, ARow, JSelectUserByDep, JSelectDepart, JDictSelectTag},
    mixins: [CmpListMixin],
    data() {
      return {
        drawerVisible:false,
        model: {},
        hoverRoomName:'会议室预定',
        //滑动状态
        isMoving:false,
        //滑动起始行列
        startRow: '',
        startCol: '',
        startTime:'',
        //滑动结束行列
        endRow: '',
        endCol: '',
        endTime:'',
        // 表单参数
        meetingDate:moment().format('YYYY-MM-DD'),
        disabledValue: true,
        loginUserName : '',
        loginUserId: '',
        departId: "",
        nowTime: moment().format("YYYY-MM-DD HH:mm:ss"),
        labelCol: {
          xs: { span: 24 },
          sm: { span: 5 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 },
        },
        form: this.$form.createForm(this),
        // 查询参数
        queryParam:{
          searchDate:moment(moment(),'YYYY-MM-DD'),
          searchRoomName:'',
        },
        // 弹窗状态
        visible: false,
        confirmLoading: false,
        confirmLoading2: false,
        // 对话框参数
        orderTime: '',
        orderRoom: '',
        orderRoomId: '',
        // 会议室信息透出
        roomList:'',
        roomListCopy: '',
        timeList: [
          { time: '8:30' },
          { time: '9:00' },
          { time: '9:30' },
          { time: '10:00' },
          { time: '10:30' },
          { time: '11:00' },
          { time: '11:30' },
          { time: '12:00' },
          { time: '12:30' },
          { time: '13:00' },
          { time: '13:30' },
          { time: '14:00' },
          { time: '14:30' },
          { time: '15:00' },
          { time: '15:30' },
          { time: '16:00' },
          { time: '16:30' },
          { time: '17:00' }
        ],
        validatorRules: {
          contactPhone:{rules: [{validator: this.validatePhone},{ required: true, message: '请输入手机号码' }]},
        },
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
          target: [{ to: 'joinMemberUserName', from: 'username' }, { to: 'joinMemberFullName', from: 'realname' }]
        },
      }
    },
    created () {
      this.loginUserName = this.$store.getters.nickname
      this.loginUserId = this.$store.getters.userId
      this.confirmLoading2 = true
      findRoomList({meetingDate:this.meetingDate})
        .then((res) => {
          this.roomList = res.result;
          this.roomListCopy = JSON.parse(JSON.stringify(this.roomList)); //将当前页面数据源深度克隆
          console.log(res.result.userInfo)
          if (res.code === 0){
            this.confirmLoading2 = false
          }
        })
    },
    methods: {
      ...mapGetters(["nickname", "avatar","userId"]),
      moment,
      onChange(date, dateString) {
        console.log(date, dateString);
        this.queryParam.searchDate = date;
        this.meetingDate = dateString;
      },
      range(start, end) {
        const result = [];
        for (let i = start; i < end; i++) {
          result.push(i);
        }
        return result;
      },
      //点击预定会议室
      order() {
        if (this.orderRoom == null || this.orderRoom == ''){
          this.$message.warning('您没有选择会议室哦')
        }else {
          // 弹出预定表单
          this.visible = true
          // 预定会议室信息
        }
      },
      myMouseDown: function(item, items,index,key){
        this.orderRoom = item.roomName
        this.orderRoomId = item.roomId
        const that = this
        if (that.endCol != null){
          that.roomList = that.roomListCopy //将深度克隆的对象 赋给页面数据源
          that.roomListCopy = JSON.parse(JSON.stringify(this.roomList)); //将当前页面数据源深度克隆
        }
        console.log("鼠标点下" + "=> 当前行是 =>" + key + "当前列是 =>" + index)
        if (items == 2){
          that.roomList[key].arrUseInfo[index].status = 0
        } else if (items == 1){
          that.$message.warning('当前会议室已经被预定')
          that.orderTime = ''
        } else if (items == 0){
          that.roomList[key].arrUseInfo[index].status = 2
          that.isMoving = true
          that.startRow = key //起始行坐标
          that.startCol = index //起始列坐标
          that.startTime = that.timeList[index].time
        }
      },
      myMouseUp:function(item, items ,index,key){
        // debugger
        const that = this
        console.log("鼠标抬起了")
        that.endCol = index //结束列坐标
        if (that.startTime != null && that.startTime != '') {
          if (that.startCol <= index) { //正常顺序
            if (that.endCol == 17) {
              that.endTime = '17:30'
            } else {
              that.endTime = that.timeList[index + 1].time
            }
            that.orderTime = that.startTime + " - " + that.endTime
          } else { //倒序
            if (that.startCol == 17) {
              that.startTime = '17:30'
              that.endTime = that.timeList[index].time
            } else {
              that.startTime = that.timeList[that.startCol].time
              that.endTime = that.timeList[index].time
            }
            that.orderTime = that.endTime + " - " + that.startTime
          }
        }else {
          that.orderTime = ''
        }
        // 弹出预定表单
        if (that.isMoving == true) {
          // 初始化选人组件字段
          that.auditUsers1.value = []
          that.auditUsers2.value = []

          this.visible = true //弹出表单
          that.isMoving = false //关闭滑动
        }
      },
      mymouseOver: function(item, items ,index,key){
        let isMoving = this.isMoving
        const that = this
        if (!isMoving) return false
        console.log("鼠标划过了" + "(" + key + "," + index + ")")
        if (items == 0){
          // that.roomList[key].arrUseInfo[index] = 0
          that.roomList[that.startRow].arrUseInfo[index].status = 2
        } else if (items == 1){
          that.$message.warning('时间有冲突，请重新选择')
          that.isMoving = false // 关闭滑动
          that.roomList = that.roomListCopy //将深度克隆的对象 赋给页面数据源
          that.roomListCopy = JSON.parse(JSON.stringify(this.roomList)); //将当前页面数据源深度克隆
        }
      },
      handleOk(e) {
        e.preventDefault();
        const that = this;
        // 触发表单验证
        that.form.validateFields((err, values) => {
          let formData = Object.assign(this.model, values);
          // 组装formData
          formData.contactId = that.loginUserId;
          formData.cancelFlag = that.cancelFlag;
          formData.meetingDate = that.meetingDate.substring(0,10);
          formData.startCol = that.startCol;
          formData.endCol = that.endCol;
          // 选人控件传值
          this.uploadMan(formData,that);
          if (!err){
            that.confirmLoading = true;
            that.axios.post('/meetingRoom/meetingRoomTimeStatus/add',{
              roomId:that.orderRoomId,
              meetingDate:that.meetingDate,
              startSort:that.startCol,
              endSort:that.endCol,
            })
              .then(function (response){
                if (response.code === 200){
                  that.$notification.success({
                    message: '您好',
                    description: response.message,
                  })
                  //  新增记录
                  that.axios.post('/meetingRoom/add',formData)
                    .then(function (response) {
                      that.confirmLoading2 = true
                      console.log(response);
                      if (response.code === 200){
                        findRoomList({meetingDate:that.meetingDate})
                          .then((res) => {
                            that.roomList = res.result;
                            that.roomListCopy = JSON.parse(JSON.stringify(that.roomList)); //将当前页面数据源深度克隆
                            if (res.code === 0){
                              that.confirmLoading2 = false
                            }
                          })
                      }
                    })
                    .catch(function (error) {
                      console.log(error);
                    });
                } else if (response.code === 10002){
                  that.$notification.warning({
                    message: '抱歉',
                    description: response.message,
                  })
                } else {
                  that.$notification.error({
                    message: '错误',
                    description: '服务器开小差了',
                  })
                }
              })
              .catch(function (error) {
              that.$notification.error({
                message: '错误',
                description: '请求出现错误',
              })
            })
            that.confirmLoading = false
            that.visible = false
            that.confirmLoading2 = true
          }
        })
      },
      close () {
        this.$emit('close');
        this.visible = false;
      },
      handleCancel(e) {
        this.close()
      },
      enter: function(item, items,index) {
        const time1 = this.timeList[index].time
        let time2 = null
        if (index < this.timeList.length-1) {
          time2 = this.timeList[index + 1].time
        }if (index === this.timeList.length-1){
          time2 = "17:30"
        }
        this.hoverRoomName = '会议室预定 【时间段：' + time1 + "—" + time2 + '】'
      },
      leave: function() {
        // this.hoverRoomName = '会议室预定'
      },
      disabledDate(current) {
        // Can not select days before today and today
        return current && current < moment().startOf('day');
        // return current && current < moment().endOf('day');
      },
      searchQuery:function() {
        this.confirmLoading2 = true
        findRoomList({meetingDate:this.meetingDate})
          .then((res) => {
          this.roomList = res.result;
          this.roomListCopy = JSON.parse(JSON.stringify(this.roomList)); //将当前页面数据源深度克隆
          console.log(res.result.userInfo)
            if (res.code === 0){
              this.confirmLoading2 = false
            }
        })
      },
      searchReset:function() {
        this.confirmLoading2 = true
        findRoomList({meetingDate:this.meetingDate})
          .then((res) => {
            this.roomList = res.result;
            this.roomListCopy = JSON.parse(JSON.stringify(this.roomList)); //将当前页面数据源深度克隆
            console.log(res.result.userInfo)
            if (res.code === 0){
              this.confirmLoading2 = false
            }
          })
        this.queryParam.searchDate = moment(moment(),'YYYY-MM-DD')
      },
      //转换百分数
     toPercent (val){
        return (Math.round(val * 10000)/100).toFixed(2) + '%';
      },
      getWidth(length){
        return this.toPercent(length)
      },
      validatePhone(rule, value, callback){
        if(!value || new RegExp(/^1[3|4|5|7|8][0-9]\d{8}$/).test(value)){
          callback();
        }else{
          callback("请输入正确格式的手机号码!");
        }
      },
      loadData() {}
    },
  }
</script>

<style scoped lang="less">
  @import '~@assets/less/common.less';
  @import '~@assets/less/topBtns.less';
  @table-title-heignt: 60px;
  @table-content-heignt: 50px;
  .active {
    width: auto;
    height: auto;
    background: #8ddd87;
  }
  .activeOthers {
    width: auto;
    height: auto;
    background: #5dd1be;
  }
  .mousedown {
    width: auto;
    height: auto;
    background: #d6def5;
    font-weight: bold;
    /*background-image: url("../../assets/img/meeting/reserved.png") ;*/
    /*background-repeat:no-repeat;*/
    /*background-origin: content-box;*/
  }
  .father-box {
    width: 100%;
  }
  .room-time-father-box {
    width: 80%;
    float: left;
  }
  /*.info-icon {*/
    /*color: #bebebe;*/
    /**/
  /*}*/
  .info-icon-order{
    color: white;
  }

  .title-box {
    width: auto;
    height: 100%;
    margin-left: 1px;
    text-align: center;
    /*background-color: #0099ff;*/
    font-family: 'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'STHeitiSC-Light', 'Microsoft YaHei', '微软雅黑', Arial,
    sans-serif;
    font-weight: bold;
    color: white;
  }
  .content-box {
    width: auto;
    height: 100%;
    text-align: center;
    font-family: 'Helvetica Neue', Helvetica, 'Hiragino Sans GB', 'STHeitiSC-Light', 'Microsoft YaHei', '微软雅黑', Arial,
    sans-serif;
    border-style:none solid solid none;
    border-width: 1q;
    border-color: #d5d5d5;
  }

  .title-room-name-box {
    width: 15%;
    height: @table-title-heignt;
    line-height: @table-title-heignt;
    float: left;
  }
  .content-room-name-box {
    width: 15%;
    height: @table-content-heignt;
    line-height: @table-content-heignt;
    float: left;
    border-style:none none none solid;
    border-width: 1q;
    border-color: #d5d5d5;
  }
  .title-contain-number{
    width: 5%;
    height: @table-title-heignt;
    line-height: @table-title-heignt;
    float: left;
  }
  .content-contain-number{
    width: 5%;
    height: @table-content-heignt;
    line-height: @table-content-heignt;
    float: left;
  }
  .title-room-time-box {
    width: 5.55%;
    height: @table-title-heignt;
    line-height: @table-title-heignt;
    float: left;
  }
  .content-room-time-box {
    width: 5.55%;
    height: @table-content-heignt;
    line-height: @table-content-heignt;
    float: left;
  }
</style>
