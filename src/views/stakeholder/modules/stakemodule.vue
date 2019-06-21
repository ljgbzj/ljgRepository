<template>
  <div class="stakemodule">
    <a-modal
      :footer="null"
      :title="title"
      :width="1000"
      :visible="visible"
      :confirmLoading="confirmLoading"
      @ok="handleOk"
      @cancel="handleCancel"
      cancelText="关闭"
    >
      <a-spin :spinning="confirmLoading">
        <a-form :form="form">
          <a-row :gutter="24">
            <a-col :span="12">
              <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="企业/个人编号">
                <a-input
                  placeholder
                  v-decorator="['inputerFullname', validatorRules.templateName]"
                />
              </a-form-item>
            </a-col>
            <a-col :span="12">
              <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="企业/个人名称">
                <a-input
                  placeholder
                  v-decorator="['inputerFullname', validatorRules.templateName]"
                />
              </a-form-item>
            </a-col>
          </a-row>
          <a-row :gutter="24">
            <a-col :span="12">
              <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="干系人类型">
                <a-select placeholder="选择" v-decorator="['type', validatorRules.templateType]">
                  <a-select-option value="0">事假</a-select-option>
                  <a-select-option value="1">病假</a-select-option>
                  <a-select-option value="2">年假</a-select-option>
                  <a-select-option value="3">婚假</a-select-option>
                  <a-select-option value="4">出差</a-select-option>
                </a-select>
              </a-form-item>
            </a-col>
            <a-col :span="12">
              <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="企业/单位性质">
                <!-- <a-input placeholder="请输入请假类型" v-decorator="['type', {}]" /> -->
                <a-select placeholder="选择" v-decorator="['type', validatorRules.templateType]">
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
            <a-col :span="8">
              <a-form-item :labelCol="labelCol3" :wrapperCol="wrapperCol3" label="联系电话">
                <a-input placeholder v-decorator="['reason', validatorRules.templateReason]"/>
              </a-form-item>
            </a-col>
            <a-col :span="8">
              <a-form-item :labelCol="labelCol3" :wrapperCol="wrapperCol3" label="传真">
                <a-input placeholder v-decorator="['reason', validatorRules.templateReason]"/>
              </a-form-item>
            </a-col>
            <a-col :span="8">
              <a-form-item :labelCol="labelCol3" :wrapperCol="wrapperCol3" label="邮编">
                <a-input placeholder v-decorator="['reason', validatorRules.templateReason]"/>
              </a-form-item>
            </a-col>
          </a-row>

          <a-form-item :labelCol="labelCol1" :wrapperCol="wrapperCol1" label="地址">
            <a-input placeholder v-decorator="['reason', validatorRules.templateReason]"/>
          </a-form-item>
          <a-form-item :labelCol="labelCol1" :wrapperCol="wrapperCol1" label="网址">
            <a-input placeholder v-decorator="['reason', validatorRules.templateReason]"/>
          </a-form-item>
          <a-row :gutter="24">
            <a-col :span="12">
              <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="开户行">
                <a-input placeholder v-decorator="['reason', validatorRules.templateReason]"/>
              </a-form-item>
            </a-col>
            <a-col :span="12">
              <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="账号">
                <a-input placeholder v-decorator="['reason', validatorRules.templateReason]"/>
              </a-form-item>
            </a-col>
          </a-row>
          <a-row :gutter="24">
            <a-col :span="12">
              <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="备注/服务范围">
                <a-input placeholder v-decorator="['reason', validatorRules.templateReason]"/>
              </a-form-item>
            </a-col>
            <a-col :span="12">
              <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="传详情">
                <a-input placeholder v-decorator="['reason', validatorRules.templateReason]"/>
              </a-form-item>
            </a-col>
          </a-row>
          <a-form-item :labelCol="labelCol1" :wrapperCol="wrapperCol1" label="附件">
            <a-input placeholder v-decorator="['reason', validatorRules.templateReason]"/>
          </a-form-item>

          <!-- 操作按钮区域 -->
          <a-form-item>
            <div class="table-operator">
              <a-button @click="handleAdd" type="primary" icon="plus">添加</a-button>
              <a-button type="primary" icon="edit">修改</a-button>
              <a-button @click="handleDelete" type="primary" icon="delete">删除</a-button>
              <a-button type="primary" icon="zoom-in">浏览</a-button>
              <a-button type="primary" icon="reload">刷新</a-button>
            </div>
          </a-form-item>

          <!-- table区域-begin -->
          <a-form-item>
            <div>
              <!-- <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">
              <i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择
              <a style="font-weight: 600">{{ selectedRowKeys.length }}</a>项
              <a style="margin-left: 24px" @click="onClearSelected">清空</a>
              </div>-->
              <a-table
                ref="table"
                size="middle"
                bordered
                rowKey="key"
                :columns="columns"
                :dataSource="dataSource"
                :pagination="false"
                :loading="loading"
                @change="handleTableChange"
                class="table"
              >
                <span slot="important" slot-scope="important">
                  <a-checkbox :checked="important" class="important"></a-checkbox>
                </span>
              </a-table>
            </div>
          </a-form-item>

          <a-row :gutter="24">
            <a-col :span="12">
              <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="录入人">
                <a-input placeholder v-decorator="['reason', validatorRules.templateReason]"/>
              </a-form-item>
            </a-col>
            <a-col :span="12">
              <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="联系电话">
                <a-input placeholder v-decorator="['reason', validatorRules.templateReason]"/>
              </a-form-item>
            </a-col>
          </a-row>
          <a-row :gutter="24">
            <a-col :span="12">
              <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="录入人部门">
                <a-input placeholder v-decorator="['reason', validatorRules.templateReason]"/>
              </a-form-item>
            </a-col>
            <a-col :span="12">
              <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="录入时间">
                <a-input placeholder v-decorator="['reason', validatorRules.templateReason]"/>
              </a-form-item>
            </a-col>
          </a-row>

          <a-form-item :labelCol="labelCol1" :wrapperCol="wrapperCol1" label="新任务通知方式">
            <a-checkbox>邮件</a-checkbox>
            <a-checkbox>手机短信</a-checkbox>
            <a-checkbox>EUC消息</a-checkbox>
          </a-form-item>
          <a-form-item :labelCol="labelCol1" :wrapperCol="wrapperCol1" label="审批人">
            <a-select placeholder="选择" v-decorator="['type', validatorRules.templateType]">
              <a-select-option value="0">事假</a-select-option>
              <a-select-option value="1">病假</a-select-option>
              <a-select-option value="2">年假</a-select-option>
              <a-select-option value="3">婚假</a-select-option>
              <a-select-option value="4">出差</a-select-option>
            </a-select>
          </a-form-item>
              <a-form-item class="btnClass">
                <a-button
                  @click="handleSave('start')"
                  icon="save"
                  v-if="model.status == undefined"
                  style="margin-right:10px"
                  class="cancel"
                >暂存</a-button>
                <a-button
                  @click="handleCancel"
                  icon="close"
                  style="margin-right:10px"
                  class="cancel"
                >取消</a-button>
                <a-button @click="handleOk" icon="check" class="confirm">提交</a-button>
              </a-form-item>
          <!-- table区域-end -->
        </a-form>
      </a-spin>
    </a-modal>
  </div>
</template>

<script>
import { httpAction, httpActionHeader } from '@/api/manage'
import { CmpListMixin } from '@/mixins/CmpListMixin'
import pick from 'lodash.pick'
import moment from 'moment'
import JSelectUserByDep from '@/components/cmpbiz/JSelectUserByDep'
import { mapGetters } from 'vuex'
import qs from 'qs'

import { setTimeout } from 'timers'

export default {
  name: 'stakemodule',
  mixins: [CmpListMixin],
  components: {
    JSelectUserByDep
  },
  data() {
    return {
      title: '操作',
      visible: false,
      model: {},
      labelCol: {
        xs: { span: 24 },
        sm: { span: 8 }
      },
      wrapperCol: {
        xs: { span: 24 },
        sm: { span: 16 }
      },
      labelCol3: {
        xs: { span: 24 },
        sm: { span: 12 }
      },
      wrapperCol3: {
        xs: { span: 24 },
        sm: { span: 12 }
      },
      labelCol1: {
        xs: { span: 24 },
        sm: { span: 4 }
      },
      wrapperCol1: {
        xs: { span: 24 },
        sm: { span: 20 }
      },

      columns: [
        {
          title: '序号',
          dataIndex: 'key',
          key: 'key',
          width: 60,
          align: 'center'
        },
        {
          title: '姓名',
          align: 'center',
          dataIndex: 'name'
        },
        {
          title: '性别',
          align: 'center',
          dataIndex: 'sex'
        },
        {
          title: '职务',
          align: 'center',
          dataIndex: 'position'
        },
        {
          title: '手机号',
          align: 'center',
          dataIndex: 'mobile'
        },
        {
          title: '办公电话',
          align: 'center',
          dataIndex: 'phone'
        },
        {
          title: 'E-MAIL',
          align: 'center',
          dataIndex: 'mail'
        },
        {
          title: '地址',
          align: 'center',
          dataIndex: 'address'
        },
        {
          title: '传真',
          align: 'center',
          dataIndex: 'fax'
        },
        {
          title: '重要干系人',
          align: 'center',
          dataIndex: 'important',
          scopedSlots: { customRender: 'important' }
        },
        {
          title: '备注',
          align: 'center',
          dataIndex: 'remarks'
        }
      ],
      dataSource: [
        {
          key: '1',
          name: '尼古拉斯·赵四',
          sex: '男',
          position: 'PM',
          mobile: '13512345678',
          phone: '010-88504321',
          mail: 'aaa1234132@163.com',
          address: '浙江省杭州市余杭区智能小镇9号楼3层',
          fax: '025-12344321',
          important: false,
          remarks: '这是一条备注'
        }
      ],
      url: {
        list: ''
      },
      confirmLoading: false,
      form: this.$form.createForm(this),
      validatorRules: {
        templateName: { rules: [{ required: true, message: '请输入请假人姓名!' }] },
        templateType: { rules: [{ required: true, message: '请选择请假类型!' }] },
        templateStartT: { rules: [{ required: true, message: '请选择开始时间!' }] },
        templateEndT: { rules: [{ required: true, message: '请选择开结束时间!' }] },
        templateReason: { rules: [{ required: true, message: '请输入请假原因!' }] },
        templateDeLeader: { rules: [{ required: true, message: '请选择部门领导!' }] },
        templatehrLeader: { rules: [{ required: true, message: '请选择人事部门领导!' }] },
        templateGeManager: { rules: [{ required: true, message: '请选择总经理!' }] },
        templateContent: { rules: [] }
      },
      url: {
        add: '/oa/leaveApplication/action',
        edit: '/oa/leaveApplication/edit'
      }
    }
  },
  created() {},
  methods: {
    ...mapGetters(['nickname', 'userInfo']),
    add() {
      this.edit({})
    },
    edit(record) {
      this.form.resetFields()
      this.model.inputerFullname = this.nickname()
      this.model = Object.assign({}, record)
      this.visible = true

      this.$nextTick(() => {
        this.form.setFieldsValue(
          pick(
            this.model,
            'inputerFullname',
            'type',
            'reason',
            'departmentLeaderUsername',
            'departmentLeaderRealname',
            'hrLeaderUsername',
            'hrLeaderRealname',
            'generalManagerUsername',
            'generalManagerRealname',
            'processDefinitionId',
            'processInstanceId',
            'remarks'
          )
        )
        //时间格式化
        this.form.setFieldsValue({ timeStart: this.model.timeStart ? moment(this.model.timeStart) : null })
        this.form.setFieldsValue({ timeEnd: this.model.timeEnd ? moment(this.model.timeEnd) : null })
        this.form.setFieldsValue({ inputerFullname: this.nickname() })
      })
    },
    close() {
      this.$emit('close')
      this.visible = false
    },
    handleOk() {
      const that = this
      // 触发表单验证
      this.form.validateFields((err, values) => {
        console.log()
        if (!err) {
          that.confirmLoading = true
          let httpurl = ''
          let method = ''
          let qsMothods = ''
          let formData = Object.assign(this.model, values)
          if (!this.model.id) {
            httpurl += this.url.add
            method = 'post'
            formData.api = '/process/startAndSubmit'
            formData.processDefinitionKey = 'leave'
          } else {
            httpurl += this.url.edit
            method = 'put'
          }
          // let formData = Object.assign(this.model, values);

          //时间格式化
          formData.timeStart = formData.timeStart ? formData.timeStart.format('YYYY-MM-DD HH:mm:ss') : null
          formData.timeEnd = formData.timeEnd ? formData.timeEnd.format('YYYY-MM-DD HH:mm:ss') : null

          console.log(formData)
          if (method == 'post') {
            httpAction(httpurl, qs.stringify(formData), method)
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
          } else {
            httpActionHeader(httpurl, JSON.stringify(formData), method)
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
      })
    },
    handleCancel() {
      this.close()
    }
  }
}
</script>

<style lang="less" scoped>
.table-operator{
  button{
    margin-right: 5px;
    background-color: #6455ff;
    color: #fff;
    border: 1px solid  #6455ff;
  }
}
.ant-col-12,
.ant-col-8 {
  padding-left: 0 !important;
  padding-right: 0 !important;
}
.ant-row {
  margin-left: 0 !important;
  margin-right: 0 !important;
  :global(.ant-form-item-control-wrapper) {
    :glabal(td) {
      vertical-align: middle;
    }
  }
  :global(.table.ant-table-wrapper) {
    .ant-table-tbody {
      background-color: red;
    }
  }
  .important{
      :global(.ant-checkbox-checked .ant-checkbox-inner){
        background-color: #6455ff;
        border-color:#6455ff;
      }
      :global(&:hover .ant-checkbox-inner){
        border-color:#6455ff;
      }
      :global(.ant-checkbox-input:focus + .ant-checkbox-inner){
        border-color:#6455ff;
      }
  }
}
.btnClass{
  text-align: center;
  button{
    background-color: #6455ff;
    color: #fff;
  }
}
</style>