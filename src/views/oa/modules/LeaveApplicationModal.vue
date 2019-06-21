<template>
  <a-modal
    :footer="null"
    :title="title"
    :width="1000"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleOk"
    @cancel="handleCancel"
    cancelText="关闭"
    :okButtonProps="model.btns ? { style: 'display:none' } : {}"
    :cancelButtonProps="model.btns ? { style: 'display:none' } : {}"
    v-dialogDrag
    :centered="true"
    :maskClosable="false">
    <!-- <div style="font-size:16px;color:rgba(25,25,25,1);">
      <a-icon :type="typeIcon" />
      {{ title }}
    </div> -->
    <a-tabs defaultActiveKey="1">
      <a-tab-pane key="1">
        <span slot="tab">
          <a-icon type="project" />
          表单详情
        </span>
        <a-spin :spinning="confirmLoading">
          <a-form :form="form" class="">        
            <!-- <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="请假人用户编码">
              <a-input placeholder="请输入请假人用户编码" v-decorator="['username', {}]" />
            </a-form-item> -->
            <a-row :gutter="24">
              <a-col :span="12">
                <a-form-item
                  :labelCol="labelCol"
                  :wrapperCol="wrapperCol"
                  label="请假人">
                  <a-input placeholder="请输入请假人姓名" v-decorator="['inputerFullname', validatorRules.templateName]" disabled/>
                </a-form-item>
              </a-col>
              <a-col :span="12">
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
              </a-col>
            </a-row>
            <a-row :gutter="24">
              <a-col :span="12">
                <a-form-item
                  :labelCol="labelCol"
                  :wrapperCol="wrapperCol"
                  label="开始时间">
                  <a-date-picker showTime format='YYYY-MM-DD HH:mm:ss' v-decorator="[ 'timeStart', validatorRules.templateStartT]" />
                </a-form-item>
              </a-col>
              <a-col :span="12">
                <a-form-item
                  :labelCol="labelCol"
                  :wrapperCol="wrapperCol"
                  label="结束时间">
                  <a-date-picker showTime format='YYYY-MM-DD HH:mm:ss' v-decorator="[ 'timeEnd', validatorRules.templateEndT]" />
                </a-form-item>
              </a-col>
            </a-row>
            <a-row :gutter="24">
              <a-col :span="24">
                <a-form-item
                  :labelCol="labelCol1"
                  :wrapperCol="wrapperCol1"
                  label="请假原因">
                  <a-textarea placeholder="请输入请假原因" :rows="4" v-decorator="[ 'reason', validatorRules.templateReason]"/>
                </a-form-item>
              </a-col>
            </a-row>
            <!--  人员选择控件 -->
            <a-row :gutter="24">
              <a-col :span="12">
                <a-form-item
                  :labelCol="labelCol"
                  :wrapperCol="wrapperCol"
                  label="部门领导">
                  <j-select-user-by-dep v-model="departmentLeaderRealname" @userName="departmentUserName"></j-select-user-by-dep>
                </a-form-item>
              </a-col>
              <a-col :span="12">
                <a-form-item
                  :labelCol="labelCol"
                  :wrapperCol="wrapperCol"
                  label="人事领导">
                  <j-select-user-by-dep v-model="hrLeaderRealname" @userName="hrUsername"></j-select-user-by-dep>
                </a-form-item>
              </a-col>
            </a-row>
            <a-row :gutter="24">
              <a-col :span="12">
                <a-form-item
                  :labelCol="labelCol"
                  :wrapperCol="wrapperCol"
                  label="总经理">
                  <j-select-user-by-dep v-model="generalManagerRealname" @userName="ManagerUserName" ></j-select-user-by-dep>
                </a-form-item>
              </a-col>
              <a-col :span="12">
                <a-form-item
                  :labelCol="labelCol"
                  :wrapperCol="wrapperCol"
                  label="状态"
                  v-show="model.status !== undefined && title == '编辑'">
                  <a-select placeholder="开启状态中" v-decorator="['status', {}]" disabled>
                    <a-select-option value="0">暂存</a-select-option>
                    <a-select-option value="1">流转中</a-select-option>
                    <a-select-option value="2">已完成</a-select-option>
                    <a-select-option value="3">废弃</a-select-option>
                  </a-select>
                </a-form-item>
              </a-col>
            </a-row>
            <a-row :gutter="24">
              <a-col :span="24">
                <a-form-item
                  :labelCol="labelCol1"
                  :wrapperCol="wrapperCol1"
                  label="上传附件">
                  <a-upload
                    :action="uploadAction"
                    listType="picture-card"
                    :headers="headers"
                    :fileList="fileList"
                    @change="handleChange"
                    @preview="handlePreview"
                    :remove="mmm"
                  >
                    <a-button>
                      <a-icon type="upload"/>
                      upload
                    </a-button>
                  </a-upload>
                  <!-- <img v-if="model.attachment" :src="getIdCardView()" alt="头像" @click="handlePreview" style="height:104px;max-width:300px"/> -->
                  <a-modal :visible="previewVisible" :footer="null" @cancel="handlePicCancel">
                    <img alt="example" style="width: 100%" :src="previewImage"/>
                  </a-modal>
                  <br/>
                </a-form-item>
              </a-col>
            </a-row>
            <a-row :gutter="24">
              <a-col :span="24">
                <a-form-item
                  :labelCol="labelCol1"
                  :wrapperCol="wrapperCol1"
                  label="备注信息">
                  <a-textarea placeholder="请输入备注信息" :rows="4" v-decorator="[ 'remarks', validatorRules.templateContent]"/>
                </a-form-item>
              </a-col>
            </a-row>
            <a-row :gutter="24">
              <a-col :span="24">
                <a-form-item
                  :labelCol="labelCol1"
                  :wrapperCol="wrapperCol1"
                  label="审批意见"
                  v-show="model.status !== undefined && model.status !== 0 && title !== '编辑'"
                  :disabled= "title == '编辑'">
                  <a-textarea :rows="4" v-decorator="[ '_taskComment', {}]" :disabled="!model.btns"/>
                </a-form-item>
              </a-col>
            </a-row>
            <a-row :gutter="24">
              <a-col :span="24">
                <a-form-item
                  :labelCol="labelCol"
                  :wrapperCol="wrapperCol"
                  class="btnClass"
                >
                  <template v-for="(placement, index) in btns">
                    <a-dropdown placement="bottomCenter" :key="index">
                      <a-button
                      style="margin-right:10px"
                      @click="onChange(placement)"
                      class="cancel">{{placement.btnName}}</a-button>
                      <a-menu slot="overlay" v-if="placement.btnApi == '/task/jump'">
                        <a-menu-item v-for="(v,k) in rollback" :key="k">
                          <div @click="goBack(v.nodeId)">{{ v.nodeName }}</div>
                        </a-menu-item>
                      </a-menu>
                    </a-dropdown>
                  </template>
                </a-form-item>
              </a-col>
            </a-row>
            <a-row :gutter="24" v-if="!btns">
              <a-col :span="24">
                <a-form-item
                  :labelCol="labelCol"
                  :wrapperCol="wrapperCol"
                  class="btnClass"
                >
                  <a-button @click="handleSave('start')" icon="diff" v-if="model.status == undefined" style="margin-right:10px" class="cancel">暂存</a-button>
                  <a-button @click="handleCancel" icon="close" style="margin-right:10px" class="cancel">取消</a-button>
                  <a-button @click="handleOk" icon="check" class="confirm">提交</a-button>
                </a-form-item>
              </a-col>
            </a-row>
          </a-form>
          <!-- <option-list :commentList="commentList" v-if="commentList.length !== undefined"></option-list> -->
          <option-list :commentList="commentList" :currentList="currentList" v-if="model.status !== undefined"></option-list>
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
            </h3>
            <s-table 
              :columns="goodsColumns" 
              :data="loadGoodsData"
              v-if="commentList"
              :pagination="false">
            </s-table>
          </div>
          <div class="proc_bg">
            <h3>
              <span>处理中</span>
            </h3>
            <s-table 
                :columns="goodsColumns1" 
                :data="loadGoodsData1"
                v-if="commentList"
                :pagination="false">
            </s-table>
          </div>
        </div>
      </a-tab-pane>
    </a-tabs>
    <!-- 通过部门筛选，选择人 -->
    <j-search-user-by-dep ref="JSearchUserByDep" @ok="onSearchDepUserCallBack"></j-search-user-by-dep>
  </a-modal>
</template>

<script>
  import { httpAction, getActionUrl, getAction } from '@/api/manage'
  import OptionList from './OptionList'
  import pick from 'lodash.pick'
  import moment from "moment"
  import JSelectUserByDep from '@/components/cmpbiz/JSelectUserByDep'
  import { mapGetters } from 'vuex'
  import qs from 'qs';
  import { ACCESS_TOKEN } from "@/store/mutation-types"
  import Vue from 'vue'
  import STable from '@/components/table/'
  import { setTimeout } from 'timers';
  import JSearchUserByDep from '@/components/cmpbiz/JSearchUserByDep'

  export default {
    name: "LeaveApplicationModal",
    components: {
      JSelectUserByDep,
      OptionList,
      STable,
      JSearchUserByDep
    },
    data () {
      return {
        title:"操作",
        typeIcon: '',
        visible: false,
        model: {},
        labelCol: {
          xs: { span: 24 },
          sm: { span: 4 }
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 }
        },
        labelCol1: {
          xs: { span: 24 },
          sm: { span: 2 }
        },
        wrapperCol1: {
          xs: { span: 24 },
          sm: { span: 20 }
        },
        // wrapperCol1: {
        //   xs: { span: 24, offset: 0 },
        //   sm: { span: 16, offset: 5 }
        // },
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
          taskComment: '/flowable/process/taskComment',
          fileUpload: window._CONFIG['domianURL'] + "/sys/common/upload",
          imgerver: window._CONFIG['domianURL'] + "/sys/common/view",
          pdferver: window._CONFIG['domianURL'] + '/sys/common/pdf/pdfPreviewIframe'
        },
        imgurl: '',
        radioStyle: '',
        btnsValue: '',
        commentList: {},
        currentList: 0,
        unfinishedList:{},
        url123: '',
        goodsColumns: [
          {
            title: '序号',
            dataIndex: '',
            key:'rowIndex',
            align:"center",
            customRender:function (t,r,index) {
              return parseInt(index)+1;
            }
          },
          {
            title: '步骤名称',
            dataIndex: 'nodeName',
            key: 'nodeName'
          },
          {
            title: '处理人',
            dataIndex: 'assigneeFullname',
            key: 'assigneeFullname',
            align:"center",
          },
          {
            title: '到达时间',
            dataIndex: 'startTime',
            key: 'startTime',
            align:"center",
          },
          {
            title: '完成时间',
            dataIndex: 'endTime',
            key: 'endTime',
            align: 'right',
            align:"center",
          },
          {
            title: '处理意见',
            dataIndex: 'num',
            key: 'num',   
          }
        ],
        goodsColumns1: [
          {
            title: '序号',
            dataIndex: '',
            key:'rowIndex',
            width: 80,
            align:"center",
            customRender:function (t,r,index) {
              return parseInt(index)+1;
            }
          },
          {
            title: '步骤名称',
            dataIndex: 'nodeName',
            key: 'nodeName'
          },
          {
            title: '当前处理',
            dataIndex: 'assigneeFullname',
            key: 'assigneeFullname',
            align:"center",
          },
          {
            title: '到达时间',
            dataIndex: 'startTime',
            key: 'startTime',
            align:"center",
          }
        ],
        arr: [], //初始化完成列表
        arr1: [], //初始化进行中列表
        selectedDepUsers: '',
        selectedDepUsersU:'',
        departmentLeaderUsername: '',
        departmentLeaderRealname: '',
        hrLeaderUsername: '',
        hrLeaderRealname: '',
        generalManagerUsername: '',
        generalManagerRealname: '',
        // 上传附件定义
        headers: {},
        fileList: [],
        previewImage: '',
        previewVisible: false, 
        attachment:''    
      }
    },
    created () {
      const token = Vue.ls.get(ACCESS_TOKEN);
      this.headers = {"X-Access-Token": token}
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

        // // 根据title初始化图标
        // if (this.title == '新增') {
        //   this.typeIcon = 'plus'
        // } else if (this.title == '编辑') {
        //   this.typeIcon = 'edit'
        // } else if (this.title == '执行') {
        //   this.typeIcon = 'form'
        // } else if (this.title == '查看') {
        //   this.typeIcon = 'eye'
        // }
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,
          'inputerFullname',
          'type',
          'reason',
          // 'departmentLeaderUsername',
          // 'departmentLeaderRealname',
          // 'hrLeaderUsername',
          // 'hrLeaderRealname',
          // 'generalManagerUsername',
          // 'generalManagerRealname',
          'remarks',
          'status',
          '_taskComment'));
        
        // 初始化选人组件字段
        if (record) {
          this.departmentLeaderRealname = this.model.departmentLeaderRealname;
          this.hrLeaderRealname = this.model.hrLeaderRealname;
          this.generalManagerRealname = this.model.generalManagerRealname;
        }
		  //时间格式化
          this.form.setFieldsValue({timeStart:this.model.timeStart?moment(this.model.timeStart):null})
          this.form.setFieldsValue({timeEnd:this.model.timeEnd?moment(this.model.timeEnd):null})
          this.form.setFieldsValue({inputerFullname: this.nickname()})
          if(this.model.status !== undefined){
            this.form.setFieldsValue({status: this.model.status.toString()})
          }
          
        });

        // 初始化上传文件
        console.log(this.model.attachment,'jige ');
        if (this.model.attachment !== undefined) {
          let upData = this.model.attachment.split("  ");
          for (let i = 0 ; i< upData.length-1 ; i++) {
            if (upData[i].substring(upData[i].length-4) == 'jpeg' || upData[i].substring(upData[i].length-3) == 'jpg' || upData[i].substring(upData[i].length-3) == 'png') {
              let fileChild ={
                uid: i,
                name: '有的',
                status: 'done',
                url: this.url.imgerver + "/" + upData[i]
              };
              this.fileList.push(fileChild);
            } else {
              let fileChild ={
                uid: i,
                name: '有的',
                status: 'done',
                url: this.url.pdferver + "/" + upData[i]
              };
              console.log(this.url.pdferver + "/" + upData[i],'lailailailaiai');
              this.fileList.push(fileChild);
            }
          }
          // fileList: [{
          //   uid: '-1',
          //   name: 'xxx.png',
          //   status: 'done',
          //   url: 'https://zos.alipayobjects.com/rmsportal/jkjgkEfvpUPVyRjUImniVslZfWPnJuuZ.png',
          // }],
        }
        //请求流程图 + 审批意见
        const that = this;
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
            this.commentList = res.result.taskListEnd.concat(res.result.taskListIng);
            this.currentList = res.result.taskListEnd.length-1;

            this.finishedList= Object.assign(res.result.taskListEnd);
            this.unfinishedList= Object.assign(res.result.taskListIng);

            // 渲染流程图tab中的数据列表
            var that = this;
            that.arr.length = 0;
            that.arr1.length = 0;

            for (let i in that.finishedList) {
                // let o = {};
                // o[i] = that.finishedList[i];
                // arr.push(o)
                that.arr.push(that.finishedList[i])
            }
            for (let i in that.unfinishedList) {
                // let o = {};
                // o[i] = that.commentList[i];
                // arr.push(o)
                that.arr1.push(that.unfinishedList[i])
            }
          }).finally(() => {
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
        this.picUrl = "";
        this.fileList=[];
      },
      handleOk () {
        const that = this;
        if(that.title == '查看') {
          // that.confirmLoading = false;
          that.close();
        } else {
          // 触发表单验证
        this.form.validateFields((err, values) => {
            if (!err) {
              that.confirmLoading = true;
              let httpurl = '';
              let method = '';
              let qsMothods = '';
              let formDataString = Object.assign(this.model, values);
              let flowDataString = {};
              if(!this.model.id){
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

              formDataString.departmentLeaderUsername = that.departmentLeaderUsername;
              formDataString.departmentLeaderRealname = that.departmentLeaderRealname;
              formDataString.hrLeaderUsername = that.hrLeaderUsername;
              formDataString.hrLeaderRealname = that.hrLeaderRealname;
              formDataString.generalManagerUsername = that.generalManagerUsername;
              formDataString.generalManagerRealname = that.generalManagerRealname;

              formDataString.attachment = this.attachment;
              let params2 = {
                flowDataString: JSON.stringify(flowDataString),
                formDataString: JSON.stringify(formDataString),
              }
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
                httpAction(httpurl,qs.stringify(params2), method).then((res)=>{
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
              flowDataString.api = '/process/start';
            }
            flowDataString.processDefinitionKey = 'leave';
            
            // let formData = Object.assign(this.model, values);
            // let formData = Object.assign(this.model, values);

            //时间格式化
            formDataString.timeStart = formDataString.timeStart?formDataString.timeStart.format('YYYY-MM-DD HH:mm:ss'):null;
            formDataString.timeEnd = formDataString.timeEnd?formDataString.timeEnd.format('YYYY-MM-DD HH:mm:ss'):null;
            
            formDataString.departmentLeaderUsername = that.departmentLeaderUsername;
            formDataString.departmentLeaderRealname = that.departmentLeaderRealname;
            formDataString.hrLeaderUsername = that.hrLeaderUsername;
            formDataString.hrLeaderRealname = that.hrLeaderRealname;
            formDataString.generalManagerUsername = that.generalManagerUsername;
            formDataString.generalManagerRealname = that.generalManagerRealname;

            formDataString.attachment = that.attachment;

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
        this.close();
        this.fileList = [];
      },
      onChange(value,id) {
        console.log()
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
        } else if (value.btnApi == '/task/jump') {
          return;
        } else if (value = 'jump'){
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
        return new Promise((resolve => {
          resolve({
            data: that.arr,
            // pageSize: 10,
            // pageNo: 1,
            // totalPage: 1,
            // totalCount: 10
          })
        })).then(res => {
          return res
        })
      },
      loadGoodsData1() {
        var that = this;
        return new Promise((resolve => {
          resolve({
            data: that.arr1,
            // pageSize: 10,
            // pageNo: 1,
            // totalPage: 1,
            // totalCount: 10
          })
        })).then(res => {
          return res
        })
      },
      onSearchDepUserCallBack(selectedDepUsers) {
        this.selectedDepUsers = selectedDepUsers.realname;
        this.selectedDepUsersU = selectedDepUsers.rusername;
      },
      //通过组织机构筛选选择用户
      onSearchDepUser() {
        this.$refs.JSearchUserByDep.showModal()
        this.selectedDepUsers = {}
        this.$refs.JSearchUserByDep.title = '根据部门查询用户'
      },
      departmentUserName(val) {
        this.departmentLeaderUsername = val;
      },
      hrUsername(val) {
        this.hrLeaderUsername = val;
      },
      ManagerUserName(val) {
        this.generalManagerUsername = val;
      },
      handleChange(info) {
        console.log(info,'zaihuisho0u ');
        this.fileList = info.fileList;
        if (this.fileList.length == 0) {
          this.fileList = info.fileList;
        } else {
          this.fileList.concat(info.fileList);
        }
        if (info.file.status === 'uploading') {
          return
        }
        if (info.file.status === 'done') {
          var response = info.file.response;
          console.log(info,'上传的文件');
          if (response.success) {
            this.picUrl += response.message + ",";
            this.attachment += response.message + "  ";
          } else {
            this.$message.warning(response.message);
          }
        }
        if (info.file.status === 'removed') {
          this.attachment = fileList;
        }
      },
      handlePreview(file) {
        if (file.url || file.thumbUrl) {
          this.previewImage = file.url || file.thumbUrl
        } else {
          this.previewImage = this.getIdCardView();
        }
        
        this.previewVisible = true
      },
      handlePicCancel() {
        this.previewVisible = false
      },
      mmm(dqw) {
        console.log(dqw,'哈哈');
      }
    },
    mounted() {
      
    },
    computed: {
      rollback() {
        return this.model.rollBackAbleNodes
      },
      btns(){
        return this.model.btns
      },
      uploadAction: function () {
        return this.url.fileUpload;
      }
    }
  }
</script>

<style lang="less" scoped>
  // 弹窗规范样式
  // 有图标的tab的下面横线样式
  .ant-tabs {
    :global(.ant-tabs-bar) {
      :global(.ant-tabs-nav-container) {
        :global(.ant-tabs-nav-scroll) {      
          :global(.ant-tabs-nav) {     
            :global(.ant-tabs-ink-bar) {
              width:24px!important;
              height:3px!important;
              border-radius:2px!important;
              margin-left:45px!important;
            }
          }
        }
      }
    }
  }

  // 按钮及边框样式
  .ant-form-item-label {
    line-height: 40px;
  }
  .table-page-search-wrapper {
    .ant-form-inline {
      .ant-form-item > :global(.ant-form-item-label) {
        line-height: 40px;
      }
    }
  }
  .ant-input {
    height: 40px;
  }
  textarea.ant-input {
    height: auto;
  }
  /* 下拉选框 */
  .ant-select {
    /* height: 40px; */
    :global(.ant-select-selection--single) {
      height: 40px;
      :global(.ant-select-selection__rendered) {
        line-height: 40px;
      }
    }
  }
  .ant-btn-primary {
    height:40px;
  }
  .ant-dropdown-trigger {
    height: 40px;
  }
  .ant-card-body .table-operator {
    display: flex;
    margin-bottom: 20px;
    vertical-align: top;
    height: 40px;
  }

  .ant-card-body .table-operator>div {
    flex: 1;
    margin-left: 14px;
  }

  .ant-card-body .table-operator .ant-alert-info {
    border: unset;
    border-radius:4px;
    background: rgba(109,98,255,0.1);
  }

  //时间选择
  .ant-calendar-picker {
    width: 100%!important;
    :global(.ant-input) {
      height: 40px;
    }
  }

  //按钮样式
  .btnClass {
    :global(.ant-form-item-control-wrapper) {
      width: 100%;
      text-align: center;
      button {
        margin: 0;
        padding: 0;
        border: 1px solid transparent;  //自定义边框
        outline: none;  //消除默认点击蓝色边框效果
      }
      .cancel {
        min-width:96px;
        height:40px;
        background:rgba(238,238,238,1);
        border-radius:4px;
        color:rgba(51,51,51,1);
        padding: 5px;
      }
      .confirm {
        min-width:96px;
        height:40px;
        background:rgba(109,98,255,1);
        border-radius:4px;
        color: rgba(255,255,255,1);
        padding: 5px;
      }
    }
  }

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