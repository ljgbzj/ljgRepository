<template>
  <a-modal
    :footer="null"
    :title="null"
    :closable="false"
    :width="1000"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleOk"
    @cancel="handleCancel"
    cancelText="关闭"
    :okButtonProps="model.btns ? { style: 'display:none' } : {}"
    :cancelButtonProps="model.btns ? { style: 'display:none' } : {}"
    v-dialogDrag
    :maskClosable="false"
    style="top:5%;"
  >
    <div class="title">
      <div>
        <img :src="title | IconUrl" />
        {{title}}
        <span>{{ nodeName ? '(' + nodeName + ')': ''}}</span>
      </div>
      <a-icon type="close" class="closeIcon" @click="handleCancel" />
    </div>
    <div class="card-container">
      <a-tabs defaultActiveKey="1" type="card">
        <a-tab-pane key="1" >
          <span slot="tab">
            <!-- <a-icon type="project" /> -->
            <img src="@/assets/img/login/detail.png" />
            表单详情
          </span>
          <a-spin :spinning="confirmLoading">
            <a-form :form="form" class>
              <a-row :gutter="24">
                <a-col :md="12" :sm="8">
                  <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="请假人">
                    <a-input
                      placeholder="请输入请假人姓名"
                      v-decorator="['inputerFullname', validatorRules.templateName]"
                      disabled
                    />
                  </a-form-item>
                </a-col>
                <a-col :md="12" :sm="8">
                  <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="请假类型">
                    <j-dict-select-tag
                      v-decorator="['type', validatorRules.templateType ]"
                      :triggerChange="true"
                      placeholder="请选择类型"
                      dictCode="leave_type"
                    />
                  </a-form-item>
                </a-col>
              </a-row>
              <a-row :gutter="24">
                <a-col :md="12" :sm="8">
                  <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="开始时间">
                    <a-date-picker
                      :disabledDate="disabledStartDate"
                      showTime
                      format="YYYY-MM-DD HH:mm:ss"
                      v-decorator="[ 'timeStart', validatorRules.templateStartT]"
                    />
                  </a-form-item>
                </a-col>
                <a-col :md="12" :sm="8">
                  <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="结束时间">
                    <a-date-picker
                      :disabledDate="disabledEndDate"
                      showTime
                      format="YYYY-MM-DD HH:mm:ss"
                      v-decorator="[ 'timeEnd', validatorRules.templateEndT]"
                    />
                  </a-form-item>
                </a-col>
              </a-row>
              <a-row :gutter="24">
                <a-col :md="24" :sm="8">
                  <a-form-item :labelCol="labelCol1" :wrapperCol="wrapperCol1" label="请假原因">
                    <a-textarea
                      placeholder="请输入请假原因"
                      :rows="4"
                      v-decorator="[ 'reason', validatorRules.templateReason]"
                    />
                  </a-form-item>
                </a-col>
              </a-row>
              <!--  人员选择控件 -->
              <a-row :gutter="24">
                <a-col :md="12" :sm="8">
                  <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="部门领导">
                    <j-select-user-new :selectedDetails="auditUsers1" @callback="setAuditUser" class="userSelect"></j-select-user-new>
                  </a-form-item>
                </a-col>
                <a-col :md="12" :sm="8">
                  <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="人事领导">
                    <j-select-user-new
                      :selectedDetails="auditUsers2"
                      @callback="setAuditUser"
                      class="userSelect"
                    ></j-select-user-new>
                  </a-form-item>
                </a-col>
              </a-row>
              <a-row :gutter="24">
                <a-col :md="12" :sm="8">
                  <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="总经理">
                    <j-select-user-new
                      :selectedDetails="auditUsers3"
                      @callback="setAuditUser"
                      class="userSelect"
                    ></j-select-user-new>
                  </a-form-item>
                </a-col>
                <a-col :md="12" :sm="8">
                  <a-form-item
                    :labelCol="labelCol"
                    :wrapperCol="wrapperCol"
                    label="状态"
                    v-show="model.status !== undefined && title == '编辑'"
                  >
                    <j-dict-select-tag
                      v-decorator="['status', {} ]"
                      :triggerChange="true"
                      dictCode="bpm_status"
                      disabled
                    />
                  </a-form-item>
                </a-col>
              </a-row>
              <a-row :gutter="24">
                <a-col :md="24" :sm="8">
                  <a-form-item
                    :labelCol="labelCol1"
                    :wrapperCol="wrapperCol1"
                    class="upload"
                    label="上传附件"
                  >
                    <a-upload
                      :action="uploadAction"
                      listType="picture"
                      :headers="headers"
                      :fileList="fileList"
                      @change="handleChange"
                      @preview="handlePreview"
                      :multiple="true"
                      class="upload-list-inline"
                    >
                      <a-button>
                        <a-icon type="upload" />上传
                      </a-button>
                    </a-upload>
                    <a-modal :visible="previewVisible" :footer="null" @cancel="handlePicCancel">
                      <img alt="example" style="width: 100%" :src="previewImage" />
                    </a-modal>
                    <br />
                  </a-form-item>
                </a-col>
              </a-row>
              <a-row :gutter="24">
                <a-col :md="24" :sm="8">
                  <a-form-item :labelCol="labelCol1" :wrapperCol="wrapperCol1" label="新任务通知">
                    <a-checkbox-group v-decorator="['notifyMethod', {initialValue: ['email']}]">
                      <a-checkbox value="email" disabled>邮件</a-checkbox>
                      <a-checkbox value="message">手机短信</a-checkbox>
                      <a-checkbox value="euc">EUC消息</a-checkbox>
                    </a-checkbox-group>
                  </a-form-item>
                </a-col>
              </a-row>
              <a-row :gutter="24">
                <a-col :md="24" :sm="8">
                  <a-form-item :labelCol="labelCol1" :wrapperCol="wrapperCol1" label="备注信息">
                    <a-textarea
                      placeholder="请输入备注信息"
                      :rows="4"
                      v-decorator="[ 'remarks', validatorRules.templateContent]"
                    />
                  </a-form-item>
                </a-col>
              </a-row>
              <a-row :gutter="24">
                <a-col :md="24" :sm="8">
                  <a-form-item
                    :labelCol="labelCol1"
                    :wrapperCol="wrapperCol1"
                    label="审批意见"
                    v-show="model.status !== undefined && model.status !== 0 && title !== '编辑' && nodeName != '填写表单'"
                    :disabled="title == '编辑'"
                  >
                    <a-textarea
                      :rows="4"
                      v-decorator="[ '_taskComment', {}]"
                      :disabled="!model.btns"
                    />
                  </a-form-item>
                </a-col>
              </a-row>
              <a-row :gutter="24" v-if="btns">
                <a-col :md="24" :sm="8">
                  <a-form-item class="btnClass">
                    <template v-for="(placement, index) in btns">
                      <a-dropdown placement="bottomCenter" :key="index">
                        <a-button
                          style="margin-right:10px"
                          @click="onChange(placement)"
                          :icon="placement.btnIcon"
                          class="cancel"
                        >{{placement.btnName}}</a-button>
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
                <a-col :md="24" :sm="8">
                  <a-form-item class="btnClass">
                    <a-button
                      @click="handleOk"
                      icon="check"
                      type="primary"
                      style="margin-right:10px"
                      class="confirm"
                    >{{model.status == undefined ? '提交' : '保存'}}</a-button>
                    <a-button
                      @click="handleSave('start')"
                      icon="diff"
                      v-if="model.status == undefined"
                      style="margin-right:10px"
                      class="cancel"
                    >暂存</a-button>
                    <a-button
                      @click="handleCancel"
                      icon="close"
                      style="margin-right:10px"
                      class="cancel"
                    >关闭</a-button>
                  </a-form-item>
                </a-col>
              </a-row>
            </a-form>
            <a-row :gutter="24">
              <a-col :md="24" :sm="8">
                <option-list
                  :commentList="commentList"
                  :currentList="currentList"
                  v-if="model.status !== undefined"
                ></option-list>
              </a-col>
            </a-row>
          </a-spin>
        </a-tab-pane>
        <a-tab-pane key="2">
          <span slot="tab">
            <!-- <a-icon type="bell" /> -->
            <img src="@/assets/img/login/process.png" alt />
            流程图
          </span>
          <div>
            <div class="proc_bg">
              <h3>
                <span>流程图</span>
              </h3>
              <img :src="urlChart" />
            </div>
            <div class="proc_bg">
              <h3>
                <span>已完成</span>
              </h3>
              <s-table
                :columns="goodsColumns"
                :data="loadGoodsData"
                v-if="commentList"
                :pagination="false"
              ></s-table>
            </div>
            <div class="proc_bg">
              <h3>
                <span>处理中</span>
              </h3>
              <s-table
                :columns="goodsColumns1"
                :data="loadGoodsData1"
                v-if="commentList"
                :pagination="false"
              ></s-table>
            </div>
          </div>
        </a-tab-pane>
      </a-tabs>
    </div>
  </a-modal>
</template>

<script>
import { httpAction, getActionUrl, getAction, httpActionHeader, downFile } from '@/api/manage'
import OptionList from './OptionList'
import { CmpListMixin } from '@/mixins/CmpListMixin'
import pick from 'lodash.pick'
import moment from 'moment'
import JSelectUserByDep from '@/components/cmpbiz/JSelectUserByDep'
import { mapGetters } from 'vuex'
import qs from 'qs'
import { ACCESS_TOKEN } from '@/store/mutation-types'
import Vue from 'vue'
import STable from '@/components/table/'
import JSelectUserNew from '@/components/cmpbiz/JSelectUserNew'
import JDictSelectTag from '@/components/dict/JDictSelectTag'
import md5 from 'md5'

export default {
  name: 'LeaveApplicationModal',
  components: {
    JSelectUserByDep,
    OptionList,
    STable,
    JSelectUserNew,
    JDictSelectTag
  },
  mixins: [CmpListMixin],
  data() {
    return {
      title: '操作',
      typeIcon: '',
      visible: false,
      model: {},
      labelCol: {
        xs: { span: 24 },
        sm: { span: 4 }
      },
      wrapperCol: {
        xs: { span: 24 },
        sm: { span: 20 }
      },
      labelCol1: {
        xs: { span: 24 },
        sm: { span: 2 }
      },
      wrapperCol1: {
        xs: { span: 24 },
        sm: { span: 22 }
      },
      confirmLoading: false,
      form: this.$form.createForm(this),
      validatorRules: {
        templateName: { rules: [{ required: true, message: '请输入请假人姓名!' }] },
        templateType: { rules: [{ required: true, message: '请选择请假类型!' }] },
        templateStartT: { rules: [{ required: true, message: '请选择开始时间!' }] },
        templateEndT: { rules: [{ required: true, message: '请选择开结束时间!' }] },
        templateReason: { rules: [{ required: true, message: '请输入请假原因!' }] },
        templateDeLeaderU: { rules: [{ required: true, message: '请选择部门领导!' }] },
        templateDeLeaderR: { rules: [{ required: true, message: '请选择部门领导!' }] },
        templatehrLeaderU: { rules: [{ required: true, message: '请选择人事部门领导!' }] },
        templatehrLeaderR: { rules: [{ required: true, message: '请选择人事部门领导!' }] },
        templateGeManagerU: { rules: [{ required: true, message: '请选择总经理!' }] },
        templateGeManagerR: { rules: [{ required: true, message: '请选择总经理!' }] },
        templateContent: { rules: [] }
      },
      url: {
        // add: "/oa/leaveApplication/action",
        add: '/flowable/action',
        edit: '/oa/leaveApplication/edit',
        chart: '/flowable/process/diagram',
        taskComment: '/flowable/process/taskComment',
        // fileUpload: window._CONFIG['domianURL'] + "/sys/common/upload",
        fileUpload: window._CONFIG['domianURL'] + '/file/uploadFile/add',
        fileFind: '/file/cmpFileGroup/findFilesByGroupId',
        imgerver: window._CONFIG['domianURL'] + '/sys/common/view',
        pdferver: window._CONFIG['domianURL'] + '/sys/common/pdf/pdfPreviewIframe',
        fileDownLoad: '/file/uploadFile/downloadFileById'
      },
      // imgurl: '',
      // radioStyle: '',
      // btnsValue: '',
      commentList: {},
      currentList: 0,
      finishedList: {},
      unfinishedList: {},
      urlChart: '',
      goodsColumns: [
        {
          title: '序号',
          dataIndex: '',
          key: 'rowIndex',
          align: 'center',
          customRender: function(t, r, index) {
            return parseInt(index) + 1
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
          align: 'center'
        },
        {
          title: '到达时间',
          dataIndex: 'startTime',
          key: 'startTime',
          align: 'center'
        },
        {
          title: '完成时间',
          dataIndex: 'endTime',
          key: 'endTime',
          align: 'right',
          align: 'center'
        },
        {
          title: '处理意见',
          dataIndex: 'num',
          key: 'num'
        }
      ],
      goodsColumns1: [
        {
          title: '序号',
          dataIndex: '',
          key: 'rowIndex',
          width: 80,
          align: 'center',
          customRender: function(t, r, index) {
            return parseInt(index) + 1
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
          align: 'center'
        },
        {
          title: '到达时间',
          dataIndex: 'startTime',
          key: 'startTime',
          align: 'center'
        }
      ],
      arr: [], //初始化完成列表
      arr1: [], //初始化进行中列表
      departmentLeaderUsername: '',
      departmentLeaderRealname: '',
      hrLeaderUsername: '',
      hrLeaderRealname: '',
      generalManagerUsername: '',
      generalManagerRealname: '',

      selectUser: ['auditUsers1', 'auditUsers2', 'auditUsers3'],
      auditUsers1: {
        colum: 'auditUsers1',
        value: [],
        target: [
          { to: 'departmentLeaderUsername', from: 'username' },
          { to: 'departmentLeaderRealname', from: 'realname' }
        ]
      },
      auditUsers2: {
        colum: 'auditUsers2',
        value: [],
        target: [{ to: 'hrLeaderUsername', from: 'username' }, { to: 'hrLeaderRealname', from: 'realname' }]
      },
      auditUsers3: {
        colum: 'auditUsers3',
        value: [],
        target: [{ to: 'generalManagerUsername', from: 'username' }, { to: 'generalManagerRealname', from: 'realname' }]
      },
      // 上传附件定义
      headers: {},
      fileList: [],
      previewImage: '',
      previewVisible: false,
      // attachment: [],
      attachment: [
        {
          groupId: '',
          fileTokens: '',
          fieldName: 'attachment',
          tableName: 'oa_leave_application'
        }
      ],
      nodeName: ''
    }
  },
  filters: {
    IconUrl(val) {
      switch (val) {
        case '新增':
          return require(`@/assets/img/login/add.png`)
          break
        case '编辑':
          return require(`@assets/img/login/edit.png`)
          break
        case '查看':
          return require(`@assets/img/login/view.png`)
          break
        case '审核':
          return require(`@assets/img/login/audit.png`)
          break
        default:
          break
      }
    }
  },
  created() {
    const token = Vue.ls.get(ACCESS_TOKEN)
    this.headers = { 'X-Access-Token': token }
    this.attachment[0].fileTokens = ''
  },
  methods: {
    ...mapGetters(['nickname']),
    add() {
      this.edit({})
    },
    edit(record) {
      this.form.resetFields()
      if (record.nodeName != undefined) {
        this.nodeName = record.nodeName
      }
      if (record.formData !== undefined) {
        this.model = Object.assign({}, record.flowData.processVar, record.flowData, record.formData, {
          taskId: record.taskId
        })
      } else {
        this.model = Object.assign({}, record)
      }
      this.visible = true
      this.$nextTick(() => {
        this.form.setFieldsValue(
          pick(
            this.model,
            'inputerFullname',
            'type',
            'reason',
            // 'departmentLeaderUsername',
            // 'departmentLeaderRealname',
            // 'hrLeaderUsername',
            // 'hrLeaderRealname',
            // 'generalManagerUsername',
            // 'generalManagerRealname',
            'notifyMethod',
            'remarks',
            'status',
            '_taskComment'
          )
        )

        // 初始化选人组件字段
        this.auditUsers1.value = []
        this.auditUsers2.value = []
        this.auditUsers3.value = []

        // 初始化选人控件
            this.initSelectMan(this,record);
        //时间格式化
        this.form.setFieldsValue({ timeStart: this.model.timeStart ? moment(this.model.timeStart) : null })
        this.form.setFieldsValue({ timeEnd: this.model.timeEnd ? moment(this.model.timeEnd) : null })
        this.form.setFieldsValue({ inputerFullname: this.nickname() })

        // 初始化新任务通知
        if (this.model.notifyMethod != undefined) {
          this.form.setFieldsValue({ notifyMethod: eval(this.model.notifyMethod) })
        }
      })
      // 初始化上传文件  0代表第1个上传附件初始化，1就代表第二个上传附件初始化，以此类推
      this.initUpload(this, 0)
      //请求流程图 + 审批意见
      // 第二个参数为流程图接口地址，第三哥参数为审批意见接口地址
      this.initChartAndComment(this, record, this.url.chart, this.url.taskComment)
    },
    loadData() {},
    close() {
      this.$emit('close')
      this.picUrl = ''
      this.fileList = []
      this.visible = false
    },
    handleOk() {
      const that = this
      if (that.title == '查看') {
        that.close()
      } else {
        // 触发表单验证
        this.form.validateFields((err, values) => {
          if (!err) {
            that.confirmLoading = true
            let httpurl = ''
            let method = ''
            let strFormData = Object.assign(this.model, values)
            let strFlowData = {}
            if (!this.model.id) {
              httpurl += this.url.add
              method = 'post'
              strFlowData.api = '/process/startAndSubmit'
              strFlowData.processDefinitionKey = 'leave'
            } else {
              httpurl += this.url.edit
              method = 'post' // put修改
            }

            //时间格式化
            strFormData.timeStart = strFormData.timeStart
              ? strFormData.timeStart.format('YYYY-MM-DD HH:mm:ss')
              : null
            strFormData.timeEnd = strFormData.timeEnd
              ? strFormData.timeEnd.format('YYYY-MM-DD HH:mm:ss')
              : null

            // 选人控件传值
            this.uploadMan(strFormData,that);

            // 上传组件
            for (let i = 0; i < that.attachment.length; i++) {
              strFormData.attachment = that.attachment[i].groupId
            }

            let params2 = {
              strFlowData: JSON.stringify(strFlowData),
              strFormData: JSON.stringify(strFormData),
              attachmentString: JSON.stringify(this.attachment)
            }
            httpAction(httpurl, qs.stringify(params2), method)
              .then(res => {
                if (res.success) {
                  that.$message.success(res.message)
                  that.$emit('ok')
                } else {
                  that.$message.warning(res.message)
                }
              })
              .finally(() => {
                that.confirmLoading = false
                that.close()
              })
          }
        })
      }
    },
    handleSave(lab, id) {
      const that = this
      // 触发表单验证
      if (lab == '/task/submit') {
        this.form.validateFields((err,values) => {
          if (!err) {
            that.confirmLoading = true;
            let httpurl = '';
            let method = '';
            let strFormData = Object.assign(this.model, values);
            httpurl+=this.url.add;
            method = 'post';
            let strFlowData = {};
            if (lab !== 'start') {
              strFlowData.api = lab;
              if (lab == '/task/jump') {
                strFlowData.targetNodeId = id;
              }
            } else {
              strFlowData.api = '/process/start';
            }
            strFlowData.processDefinitionKey = 'leave';

            //时间格式化
            strFormData.timeStart = strFormData.timeStart?strFormData.timeStart.format('YYYY-MM-DD HH:mm:ss'):null;
            strFormData.timeEnd = strFormData.timeEnd?strFormData.timeEnd.format('YYYY-MM-DD HH:mm:ss'):null;
            
            // 选人控件
            strFormData = this.uploadMan(strFormData,that);

            // 上传组件
            for (let i = 0; i<this.attachment.length; i++) {
              strFormData.attachment = that.attachment[i].groupId
            }

            let params1 = {
              strFlowData: JSON.stringify(strFlowData),
              strFormData: JSON.stringify(strFormData),
              strAttachment: JSON.stringify(that.attachment)
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
      } else {
        if (this.form.getFieldValue('type') == undefined) {
        this.$message.warning('请假类型不能为空')
      } else {
        let values = this.form.getFieldsValue()
        that.confirmLoading = true
        let httpurl = ''
        let method = ''
        let strFormData = Object.assign(this.model, values)
        httpurl += this.url.add
        method = 'post'
        let strFlowData = {}
        if (lab !== 'start') {
          strFlowData.api = lab
          if (lab == '/task/jump') {
            strFlowData.targetNodeId = id
          }
        } else {
          strFlowData.api = '/process/start'
        }
        strFlowData.processDefinitionKey = 'leave'

        //时间格式化
        strFormData.timeStart = strFormData.timeStart
          ? strFormData.timeStart.format('YYYY-MM-DD HH:mm:ss')
          : null
        strFormData.timeEnd = strFormData.timeEnd ? strFormData.timeEnd.format('YYYY-MM-DD HH:mm:ss') : null

        // 选人控件
        this.uploadMan(strFormData,this);

        // 上传组件
        for (let i = 0; i < this.attachment.length; i++) {
          strFormData.attachment = that.attachment[i].groupId
        }

        let params1 = {
          strFlowData: JSON.stringify(strFlowData),
          strFormData: JSON.stringify(strFormData),
          attachmentString: JSON.stringify(that.attachment)
        }
        httpAction(httpurl, qs.stringify(params1), method)
          .then(res => {
            if (res.success) {
              that.$message.success(res.message)
              that.$emit('ok')
            } else {
              that.$message.warning(res.message)
            }
          })
          .finally(() => {
            that.confirmLoading = false
            that.close()
          })
        }
      }
    },
    handleCancel() {
      this.close()
      this.fileList = []
    },
    onChange(value, id) {
      var lab = ''
      if (value.btnApi == '/process/save') {
        lab = '/process/save'
        this.handleSave(lab)
      } else if (value.btnApi == '/task/submit') {
        lab = '/task/submit'
        this.handleSave(lab)
      } else if (value.btnApi == '/task/audit') {
        lab = '/task/audit'
        this.handleSave(lab)
      } else if (value.btnApi == '/process/delete') {
        lab = '/process/delete'
        this.handleSave(lab)
      } else if (value.btnApi == '/task/jump') {
        return
      } else if ((value = 'jump')) {
        lab = '/task/jump'
        this.handleSave(lab, id)
      }
    },
    goBack(id) {
      this.onChange('jump', id)
    },
    // 加载数据方法 必须为 Promise 对象
    loadGoodsData() {
      var that = this
      return new Promise(resolve => {
        resolve({
          data: that.arr
        })
      }).then(res => {
        return res
      })
    },
    loadGoodsData1() {
      var that = this
      return new Promise(resolve => {
        resolve({
          data: that.arr1
        })
      }).then(res => {
        return res
      })
    },
    handleChange(info) {
      //调用改变方法
      this.handleChange1(info, this, 0)
    },
    handlePicCancel() {
      this.previewVisible = false
    },
    // 时间选择器的禁用封装
    disabledStartDate(startValue) {
      const endValue = this.form.getFieldValue('timeEnd')
      if (!startValue || !endValue) {
        return false
      }
      return startValue.valueOf() > endValue.valueOf()
    },
    disabledEndDate(endValue) {
      const startValue = this.form.getFieldValue('timeStart')
      if (!endValue || !startValue) {
        return false
      }
      return startValue.valueOf() >= endValue.valueOf()
    }
  },
  computed: {
    rollback() {
      return this.model.rollBackAbleNodes
    },
    btns() {
      return this.model.btns
    },
    uploadAction: function() {
      return this.url.fileUpload
    }
  }
}
</script>

<style lang="less" scoped>
@import '~@assets/less/modal.less';
</style>