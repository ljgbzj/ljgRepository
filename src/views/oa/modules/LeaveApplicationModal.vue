<template>
  <a-modal
    :title="title"
    :width="800"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleOk"
    @cancel="handleCancel"
    cancelText="关闭"
    :okButtonProps="model.btns ? { style: 'display:none' } : {}"
    :cancelButtonProps="model.btns ? { style: 'display:none' } : {}">
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
              <a-input placeholder="请输入部门领导" v-decorator="['departmentLeaderUsername', validatorRules.templateDeLeaderR]" />
            </a-form-item>
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="部门领导">
              <a-input placeholder="请输入部门领导" v-decorator="['departmentLeaderRealname', validatorRules.templateDeLeaderR]" />
            </a-form-item>
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="人事部门领导">
              <a-input placeholder="请输入人事部门领导" v-decorator="['hrLeaderUsername', validatorRules.templatehrLeaderU]" />
            </a-form-item>
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="人事部门领导">
              <a-input placeholder="请输入人事部门领导" v-decorator="['hrLeaderRealname', validatorRules.templatehrLeaderR]" />
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
              <a-input placeholder="请输入总经理" v-decorator="['generalManagerUsername', validatorRules.templateGeManagerU]" />
            </a-form-item>
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="总经理">
              <a-input placeholder="请输入总经理" v-decorator="['generalManagerRealname', validatorRules.templateGeManagerR]" />
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
              <a-textarea placeholder="请输入备注信息" :rows="4" v-decorator="[ 'remarks', validatorRules.templateContent]"/>
            </a-form-item>
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="状态"
              v-if="model.status !== undefined">
              <a-select placeholder="开启状态中" v-decorator="['status', {}]" disabled>
                <a-select-option value="0">暂存</a-select-option>
                <a-select-option value="1">流转中</a-select-option>
                <a-select-option value="2">已完成</a-select-option>
                <a-select-option value="3">废弃</a-select-option>
              </a-select>
            </a-form-item>
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="审批意见"
              v-if="model.status !== undefined">
              <a-textarea :rows="4" v-decorator="[ '_taskComment', {}]" :disabled="!model.btns"/>
            </a-form-item>
            <a-form-item
              :wrapperCol="wrapperCol1"
            >
              <template v-for="(placement, index) in btns">
                <a-dropdown placement="bottomCenter" :key="index">
                  <a-button
                  style="{ 'margin-right': '10px' }"
                  @click="onChange(placement)">{{placement.btnName}}</a-button>
                  <a-menu slot="overlay" v-if="placement.btnApi == '/task/jump'">
                    <a-menu-item v-for="(v,k) in rollback" :key="k">
                      <div @click="goBack(v.nodeId)">{{ v.nodeName }}</div>
                    </a-menu-item>
                  </a-menu>
                </a-dropdown>
              </template>
            </a-form-item>
            <a-form-item
              :wrapperCol="wrapperCol1"
              v-if="model.status == undefined"
            >
              <a-button type="primary" @click="handleSave('start')">暂存</a-button>
            </a-form-item>
          </a-form>
          <!-- <option-list :commentList="commentList" v-if="commentList.length !== undefined"></option-list> -->
          <option-list :commentList="commentList" v-if="model.status !== undefined"></option-list>
        </a-spin>
      </a-tab-pane>
      <a-tab-pane key="2" forceRender>
        <span slot="tab">
          <a-icon type="bell" />
          流程图
        </span>
        <div>
          <div class="proc_bg">
            <h3>
              <span>流程图</span>
            </h3>
            <!-- <img :src="imgUrl"/> -->
            <img :src="url123"/>
          </div>
          <div class="proc_bg">
            <h3>
              <span>已完成</span>
              <s-table 
                :columns="goodsColumns" 
                :data="loadGoodsData"
                v-if="loadGoodsData">
              </s-table>
            </h3>
          </div>
          <div class="proc_bg">
            <h3>
              <span>处理中</span>
            </h3>
            <s-table 
                :columns="goodsColumns1" 
                :data="loadGoodsData1">
            </s-table>
          </div>
        </div>
      </a-tab-pane>
    </a-tabs>
  </a-modal>
</template>

<script>
  import { httpAction, httpActionHeader, getActionUrl, getAction } from '@/api/manage'
  import OptionList from './OptionList'
  import pick from 'lodash.pick'
  import moment from "moment"
  import JSelectUserByDep from '@/components/cmpbiz/JSelectUserByDep'
  import { mapGetters } from 'vuex'
  import qs from 'qs';
  import { ACCESS_TOKEN } from "@/store/mutation-types"
  import Vue from 'vue'
  import STable from '@/components/table/'

  export default {
    name: "LeaveApplicationModal",
    components: {
      JSelectUserByDep,
      OptionList,
      STable
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
        wrapperCol1: {
          xs: { span: 24, offset: 0 },
          sm: { span: 16, offset: 5 }
        },
        confirmLoading: false,
        form: this.$form.createForm(this),
        validatorRules:{
          templateName: {rules: [{required: true, message: '请输入请假人姓名!'}]},
          templateType: {rules: [{required: true, message: '请选择请假类型!'}]},
          templateStartT: {rules: [{required: true, message: '请选择开始时间!'}]},
          templateEndT: {rules: [{required: true, message: '请选择开结束时间!'}]},
          templateReason: {rules: [{required: true, message: '请输入请假原因!'}]},
          templateDeLeaderU: {rules: [{required: true, message: '请选择部门领导!'}]},
          templateDeLeaderR: {rules: [{required: true, message: '请选择部门领导!'}]},
          templatehrLeaderU: {rules: [{required: true, message: '请选择人事部门领导!'}]},
          templatehrLeaderR: {rules: [{required: true, message: '请选择人事部门领导!'}]},
          templateGeManagerU: {rules: [{required: true, message: '请选择总经理!'}]},
          templateGeManagerR: {rules: [{required: true, message: '请选择总经理!'}]},
          templateContent: {rules: []},
        },
        url: {
          // add: "/oa/leaveApplication/action",
          add: "/flowable/action",
          edit: "/oa/leaveApplication/edit",
          chart: '/flowable/process/diagram',
          taskComment: '/flowable/process/taskComment'
        },
        imgurl: '',
        radioStyle: '',
        btnsValue: '',
        commentList: {},
        commentList1:{},
        url123: '',
        goodsColumns: [
          {
            title: '步骤名称',
            dataIndex: 'nodeName',
            key: 'nodeName'
          },
          {
            title: '处理人',
            dataIndex: 'assigneeFullname',
            key: 'assigneeFullname'
          },
          {
            title: '到达时间',
            dataIndex: 'startTime',
            key: 'startTime'
          },
          {
            title: '完成时间',
            dataIndex: 'endTime',
            key: 'endTime',
            align: 'right'
          },
          {
            title: '处理意见',
            dataIndex: 'num',
            key: 'num',
            align: 'right'
          }
        ],
        goodsColumns1: [
          {
            title: '步骤名称',
            dataIndex: 'nodeName',
            key: 'nodeName'
          },
          {
            title: '当前处理',
            dataIndex: 'assigneeFullname',
            key: 'assigneeFullname'
          },
          {
            title: '到达时间',
            dataIndex: 'startTime',
            key: 'startTime'
          }
        ]
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
        if(record.formData !== undefined) {
          this.model = Object.assign({},record.flowData.processVar, record.flowData, record.formData, {taskId: record.taskId});
        } else {
          this.model = Object.assign({}, record);
        }
        this.visible = true;

        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,
          'inputerFullname',
          'type',
          'reason',
          'departmentLeaderUsername',
          'departmentLeaderRealname',
          'hrLeaderUsername',
          'hrLeaderRealname',
          'generalManagerUsername',
          'generalManagerRealname',
          'remarks',
          'status',
          '_taskComment'));
		  //时间格式化
          this.form.setFieldsValue({timeStart:this.model.timeStart?moment(this.model.timeStart):null})
          this.form.setFieldsValue({timeEnd:this.model.timeEnd?moment(this.model.timeEnd):null})
          this.form.setFieldsValue({inputerFullname: this.nickname()})
          if(this.model.status !== undefined){
            this.form.setFieldsValue({status: this.model.status.toString()})
          }
          
        });
        //请求流程图 + 审批意见
        const that = this;
        // console.log(record.,'带我去皮带我去');
        if(JSON.stringify(record) !== "{}") {
          let params = {
            // processDefinitionId: that.model.processDefinitionId,
            // processInstanceId: that.model.processInstanceId
            id: that.model.id
          };
          let httpGetUrlC = that.url.chart;
          let httpGetUrlTc = that.url.taskComment;
          getActionUrl(httpGetUrlC,params).then((res)=>{
            this.url123 =  'data:image/png;base64,' + btoa(new Uint8Array(res).reduce((data, byte) => data + String.fromCharCode(byte), ''));
          }).finally(() => {
            // that.confirmLoading = false;
            // that.close();
          })
          getAction(httpGetUrlTc, { id: that.model.id }).then((res)=>{
            console.log(res,'进来吃口药');
            this.commentList = res.result;
            this.commentList= Object.assign(res.result.taskListEnd);
            this.commentList1= Object.assign(res.result.taskListIng);
            console.log(this.commentList,'wqedwqdqwdwqdwqdqwdqdwqd');
          }).finally(() => {
            this.loadGoodsData();
          })
        } else {
          let params = {
            processDefinitionKey: 'leave'
          };
          let httpGetUrlC = that.url.chart;
          getActionUrl(httpGetUrlC, params).then((res)=>{
            this.url123 =  'data:image/png;base64,' + btoa(new Uint8Array(res).reduce((data, byte) => data + String.fromCharCode(byte), ''));
          }).finally(() => {
            // that.confirmLoading = false;
            // that.close();
          })
        }

      },
      close () {
        this.$emit('close');
        this.visible = false;
      },
      handleOk () {
        const that = this;
        if(that.title == '查看') {
          // that.confirmLoading = false;
          that.close();
        } else {
          // 触发表单验证
        this.form.validateFields((err, values) => {
          console.log(values,'这是空闲的千万家门店屁哦我去额度为哦');
            if (!err) {
              that.confirmLoading = true;
              let httpurl = '';
              let method = '';
              let qsMothods = '';
              let formDataString = Object.assign(this.model, values);
              let flowDataString = {};
              console.log('我道歉我ID那我去打');
              if(!this.model.id){
                console.log('dqwdnwqd ')
                httpurl+=this.url.add;
                method = 'post';
                flowDataString.api = '/process/startAndSubmit';
                flowDataString.processDefinitionKey = 'leave';
              }else{
                httpurl+=this.url.edit;
                method = 'put';
              }
              // let formData = Object.assign(this.model, values);

              //时间格式化
              formDataString.timeStart = formDataString.timeStart?formDataString.timeStart.format('YYYY-MM-DD HH:mm:ss'):null;
              formDataString.timeEnd = formDataString.timeEnd?formDataString.timeEnd.format('YYYY-MM-DD HH:mm:ss'):null;

              let params2 = {
                flowDataString: JSON.stringify(flowDataString),
                formDataString: JSON.stringify(formDataString),
              }
              console.log(params2,'2222222');
              if(method == 'post'){
                httpAction(httpurl,qs.stringify(params2),method).then((res)=>{
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
                httpActionHeader(httpurl,JSON.stringify(params2), method).then((res)=>{
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
        }
      },
      handleSave (lab,id) {
        const that = this;
        // 触发表单验证
        this.form.validateFields((err, values) => {
          if (!err) {
            that.confirmLoading = true;
            let httpurl = '';
            let method = '';
            let qsMothods = '';
            let formDataString = Object.assign(this.model, values);
            httpurl+=this.url.add;
            method = 'post';
            let flowDataString = {};
            // let formDataString = {};
            if (lab !== 'start') {
              flowDataString.api = lab;
              if (lab == '/task/jump') {
                flowDataString.targetNodeId = id;
              }
            } else {
              console.log('d带我去on第五期');
              flowDataString.api = '/process/start';
            }
            flowDataString.processDefinitionKey = 'leave';
            
            // let formData = Object.assign(this.model, values);
            // let formData = Object.assign(this.model, values);

            //时间格式化
            formDataString.timeStart = formDataString.timeStart?formDataString.timeStart.format('YYYY-MM-DD HH:mm:ss'):null;
            formDataString.timeEnd = formDataString.timeEnd?formDataString.timeEnd.format('YYYY-MM-DD HH:mm:ss'):null;
            
            let params1 = {
              flowDataString: JSON.stringify(flowDataString),
              formDataString: JSON.stringify(formDataString),
            }
            httpAction(httpurl,qs.stringify(params1),method).then((res)=>{
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
      onChange(value,id) {
        console.log(value);
        var lab = '';
        if(value.btnApi == '/process/save'){
          lab = '/process/save';
          this.handleSave(lab); 
        } else if (value.btnApi == '/task/submit') {
          lab = '/task/submit';
          this.handleSave(lab);
        } else if (value.btnApi == '/task/audit') {
          lab = '/task/audit';
          this.handleSave(lab);
        } else if (value.btnApi == '/process/delete') {
          lab = '/process/delete';
          this.handleSave(lab);
        } else if (value = 'jump'){
          console.log('进来了');
          lab = '/task/jump';
          this.handleSave(lab,id); 
        }
      },
      goBack(id) {
        this.onChange('jump',id);
      },
      // 加载数据方法 必须为 Promise 对象
      loadGoodsData() {
        var that = this;
        var arr = []
        for (let i in that.commentList) {
            // let o = {};
            // o[i] = that.commentList[i];
            // arr.push(o)
            arr.push(that.commentList[i])
        }
        return new Promise((resolve => {
          resolve({
            data: arr,
            pageSize: 10,
            pageNo: 1,
            totalPage: 1,
            totalCount: 10
          })
        })).then(res => {
          return res
        })
      },
      loadGoodsData1() {
        var that = this;
        var arr = []
        for (let i in that.commentList1) {
            // let o = {};
            // o[i] = that.commentList[i];
            // arr.push(o)
            arr.push(that.commentList1[i])
        }
        return new Promise((resolve => {
          resolve({
            data: arr,
            pageSize: 10,
            pageNo: 1,
            totalPage: 1,
            totalCount: 10
          })
        })).then(res => {
          return res
        })
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
  .proc_bg {
    margin-top: 10px;
    padding: 10px 15px;
    height: auto;
    overflow: hidden;
    background: #FFF;
    border: 1px solid #EEE;
    -moz-border-radius: 6px 6px 6px 6px;
    -webkit-border-radius: 6px 6px 6px 6px;
    border-radius: 6px 6px 6px 6px;
    ul li{
      float: left;
      margin-left: 22px;
      display: block;
      height: 18px;
      line-height: 18px;
      color: #888;
      text-indent: 30px;
    }
    img {
      width: 100%;
      height: 100%;
    }
  }
</style>