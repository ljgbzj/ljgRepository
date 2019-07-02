<template>
  <div class="stakemodule">
    <a-modal
      :footer="null"
      :title="title"
      :width="1000"
      :visible="visible"
      :maskClosable="false"
      :confirmLoading="confirmLoading"
      @ok="handleOk"
      @cancel="handleCancel"
      cancelText="关闭"
    >
      <a-spin :spinning="confirmLoading">
        <!-- 主表单区域 -->
        <a-form :form="form">
          <a-row :gutter="24">
            <a-col :span="12">
              <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="企业/个人编号">
                <a-input placeholder v-decorator="['companyCode', {}]" />
              </a-form-item>
            </a-col>
            <a-col :span="12">
              <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="企业/个人名称">
                <a-input placeholder v-decorator="['companyName', {}]" />
              </a-form-item>
            </a-col>
          </a-row>
          <a-row :gutter="24">
            <a-col :span="12">
              <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="干系人类型">
                <!-- <j-dict-select-tag v-model="stakeholderType" placeholder="请选择类型" dictCode="stakeholder_type" /> -->
                <a-select placeholder="选择" v-decorator="['stakeholderType', {initialValue: ['1']}]">
                  <a-select-option value="0">干系人类型1</a-select-option>
                  <a-select-option value="1">干系人类型2</a-select-option>
                </a-select>
              </a-form-item>
            </a-col>
            <a-col :span="12">
              <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="企业/单位性质">
                <a-select placeholder="选择" v-decorator="['companyNature', {initialValue: ['0']}]">
                  <a-select-option value="0">性质1</a-select-option>
                  <a-select-option value="1">性质2</a-select-option>
                </a-select>
              </a-form-item>
            </a-col>
          </a-row>
          <a-row :gutter="24">
            <a-col :span="8">
              <a-form-item :labelCol="labelCol3" :wrapperCol="wrapperCol3" label="联系电话">
                <a-input placeholder v-decorator="['phoneNum', {}]" />
              </a-form-item>
            </a-col>
            <a-col :span="8">
              <a-form-item :labelCol="labelCol3" :wrapperCol="wrapperCol3" label="传真">
                <a-input placeholder v-decorator="['fax', {}]" />
              </a-form-item>
            </a-col>
            <a-col :span="8">
              <a-form-item :labelCol="labelCol3" :wrapperCol="wrapperCol3" label="邮编">
                <a-input placeholder v-decorator="['postCode', {}]" />
              </a-form-item>
            </a-col>
          </a-row>

          <a-form-item :labelCol="labelCol1" :wrapperCol="wrapperCol1" label="地址">
            <a-input placeholder v-decorator="['address', {}]" />
          </a-form-item>
          <a-form-item :labelCol="labelCol1" :wrapperCol="wrapperCol1" label="网址">
            <a-input placeholder v-decorator="['website', {}]" />
          </a-form-item>
          <a-row :gutter="24">
            <a-col :span="12">
              <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="开户行">
                <a-input placeholder v-decorator="['openingBank', {}]" />
              </a-form-item>
            </a-col>
            <a-col :span="12">
              <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="账号">
                <a-input placeholder v-decorator="['accountNumber', {}]" />
              </a-form-item>
            </a-col>
          </a-row>
          <a-form-item :labelCol="labelCol1" :wrapperCol="wrapperCol1" label="备注/服务范围">
            <a-input placeholder v-decorator="['remarks', {}]" />
          </a-form-item>
          <a-form-item :labelCol="labelCol1" :wrapperCol="wrapperCol1" label="上传附件">
            <a-input placeholder v-decorator="['attachment', {}]" />
          </a-form-item>

          <!-- 操作按钮区域 -->
          <!-- <a-form-item>
            <div class="table-operator">
              <a-button @click="handleAdd" type="primary" icon="plus">添加</a-button>
              <a-button type="primary" icon="edit">修改</a-button>
              <a-button @click="handleDelete" type="primary" icon="delete">删除</a-button>
              <a-button type="primary" icon="zoom-in">浏览</a-button>
              <a-button type="primary" icon="reload">刷新</a-button>
            </div>
          </a-form-item>-->

          <!-- table区域-begin -->
          <!-- <a-form-item>
            <div>
              <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">
              <i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择
              <a style="font-weight: 600">{{ selectedRowKeys.length }}</a>项
              <a style="margin-left: 24px" @click="onClearSelected">清空</a>
              </div>
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
          </a-form-item>-->
          <a-tabs v-model="activeKey" @change="handleChangeTabs">
            <a-tab-pane tab="干系人明细" :key="refKeys[0]" :forceRender="true">
              <j-editable-table
                :ref="refKeys[0]"
                :loading="stakeholderDetailTable.loading"
                :columns="stakeholderDetailTable.columns"
                :dataSource="stakeholderDetailTable.dataSource"
                :maxHeight="300"
                :rowNumber="true"
                :rowSelection="true"
                :actionButton="true"
              />
            </a-tab-pane>
          </a-tabs>

          <a-row :gutter="24">
            <a-col :span="12">
              <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="录入人">
                <a-input placeholder v-decorator="['inputerFullname', {}]" />
              </a-form-item>
            </a-col>
            <a-col :span="12">
              <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="联系电话">
                <a-input placeholder v-decorator="['inputerPhoneNum', {}]" />
              </a-form-item>
            </a-col>
          </a-row>
          <a-row :gutter="24">
            <a-col :span="12">
              <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="录入人部门">
                <a-input placeholder v-decorator="['inputerDeptName', {}]" />
              </a-form-item>
            </a-col>
            <a-col :span="12">
              <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="录入时间">
                <!-- <a-input placeholder v-decorator="['inputerTime', {}]"/> -->
                <a-date-picker
                  showTime
                  format="YYYY-MM-DD HH:mm:ss"
                  placeholder="录入时间"
                  v-decorator="['inputerTime', {}]"
                />
              </a-form-item>
            </a-col>
          </a-row>

          <a-form-item :labelCol="labelCol1" :wrapperCol="wrapperCol1" label="新任务通知方式">
            <a-checkbox-group v-decorator="['notifyMethod', {initialValue: ['0']}]">
              <a-checkbox value="0" disabled>邮件(默认必选)</a-checkbox>
              <a-checkbox value="1">手机短信</a-checkbox>
              <a-checkbox value="2">EUC消息</a-checkbox>
            </a-checkbox-group>
          </a-form-item>
          <a-form-item :labelCol="labelCol1" :wrapperCol="wrapperCol1" label="审批人">
            <!-- <a-select placeholder="选择" v-decorator="['subcontractor', {}]">
              <a-select-option value="0">审批人</a-select-option>
            </a-select>-->
            <a-input placeholder v-decorator="['subcontractor', {}]" />
          </a-form-item>
          <a-form-item class="btnClass">
            <a-button @click="handleSave" icon="save" class="cancel">暂存</a-button>
            <a-button @click="handleCancel" icon="close" class="cancel">取消</a-button>
            <a-button @click="handleOk" icon="check" class="confirm">提交</a-button>
          </a-form-item>
          <!-- table区域-end -->
        </a-form>
      </a-spin>
    </a-modal>
  </div>
</template>

<script>
import moment from 'moment'
import pick from 'lodash.pick'
import { FormTypes } from '@/utils/JEditableTableUtil'
import { JEditableTableMixin } from '@/mixins/JEditableTableMixin'
import { VALIDATE_NO_PASSED, getRefPromise, validateFormAndTables } from '@/utils/JEditableTableUtil'
import JDictSelectTag from '@/components/dict/JDictSelectTag.vue'
import { httpAction } from '@/api/manage'

export default {
  name: 'stakemodule',
  mixins: [JEditableTableMixin],
  components: {
    JDictSelectTag
  },
  data() {
    return {
      // 新增时子表默认添加几行空数据
      addDefaultRowNum: 1,
      validatorRules: {},
      refKeys: ['stakeholderDetail'],
      activeKey: 'stakeholderDetail',

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
      stakeholderDetailTable: {
        loading: false,
        dataSource: [],
        columns: [
          {
            title: '姓名',
            key: 'name',
            type: FormTypes.input,
            defaultValue: '',
            placeholder: '${title}'
          },
          {
            title: '性别',
            key: 'sex',
            type: FormTypes.input,
            defaultValue: '',
            placeholder: '${title}'
          },
          {
            title: '职务',
            key: 'job',
            type: FormTypes.input,
            defaultValue: '',
            placeholder: '${title}'
          },
          {
            title: '手机号',
            key: 'phoneNum',
            type: FormTypes.inputNumber,
            defaultValue: '',
            placeholder: '${title}'
          },
          {
            title: '办公电话',
            key: 'telephoneNum',
            type: FormTypes.inputNumber,
            defaultValue: '',
            placeholder: '${title}'
          },
          {
            title: '电子邮箱',
            key: 'email',
            type: FormTypes.input,
            defaultValue: '',
            placeholder: '${title}'
          },
          {
            title: '地址',
            key: 'address',
            type: FormTypes.input,
            defaultValue: '',
            placeholder: '${title}'
          },
          {
            title: '传真',
            key: 'fax',
            type: FormTypes.input,
            defaultValue: '',
            placeholder: '${title}'
          },
          {
            title: '是否重要',
            key: 'important',
            type: FormTypes.checkbox,
            defaultValue: '',
            placeholder: '${title}'
          },
          {
            title: '备注',
            key: 'remark',
            type: FormTypes.input,
            defaultValue: '',
            placeholder: '${title}'
          }
        ]
      },
      url: {
        add: '/stakeholder/stakeholder/add',
        edit: '/flowable/action',
        stakeholderDetail: {
          list: '/stakeholder/stakeholder/queryStakeholderDetailByMainId'
        }
      }
    }
  },
  created() {},
  methods: {
    /** 调用完edit()方法之后会自动调用此方法 */
    editAfter() {
      this.$nextTick(() => {
        this.form.setFieldsValue(
          pick(
            this.model,
            'stakeholderType',
            'companyCode',
            'companyName',
            'sourceType',
            'companyNature',
            'phoneNum',
            'fax',
            'postCode',
            'address',
            'website',
            'openingBank',
            'accountNumber',
            'remarks',
            'subcontractorName',
            'subcontractorFullname',
            'inputerName',
            'inputerFullname',
            'inputerPhoneNum',
            'inputerDeptCode',
            'inputerDeptName',
            'attachment',
            'notifyMethod',
            'status',
            'corpCode',
            'prjCode'
          )
        )
        // 时间格式化
      })
      // 加载子表数据
      if (this.model.id) {
        let params = { id: this.model.id }
        this.requestSubTableData(this.url.stakeholderDetail.list, params, this.stakeholderDetailTable)
      }
    },

    /** 整理成formData */
    classifyIntoFormData(allValues) {
      let main = Object.assign(this.model, allValues.formValue)
      //时间格式化
      return {
        ...main, // 展开
        stakeholderDetailList: allValues.tablesValue[0].values
      }
    },
    handleOk() {
      /** 触发表单验证 */
      this.getAllTable()
        .then(tables => {
          /** 一次性验证主表和所有的次表 */
          return validateFormAndTables(this.form, tables)
        })
        .then(allValues => {
          if (typeof this.classifyIntoFormData !== 'function') {
            throw this.throwNotFunction('classifyIntoFormData')
          }
          let formData = this.classifyIntoFormData(allValues)

          formData.stakeholderType = formData.stakeholderType ? '干系人类型1' : '干系人类型2'
          formData.companyNature = formData.companyNature ? '性质1' : '性质2'

          // important值true或false转换为1或0
          let len = formData.stakeholderDetailList.length
          for (let i = 0; i < len; i++) {
            formData.stakeholderDetailList[i].important = formData.stakeholderDetailList[i].important ? 1 : 0
          }

          // 发起请求
          console.log(allValues, 'allValues')
          console.log(formData, 'formData')
          let data = {}
          data.flowDataString = Object.assign(
            {},
            {
              api: '/process/startAndSubmit',
              processDefinitionKey: 'stakeholder'
            }
          )
          data.formDataString = Object.assign({}, formData)
          console.log(data, 'data')
          data = JSON.stringify(data)
          console.log(data, 'dataString')
          return this.request(data)
        })
        .catch(e => {
          if (e.error === VALIDATE_NO_PASSED) {
            // 如果有未通过表单验证的子表，就自动跳转到它所在的tab
            this.activeKey = e.index == null ? this.activeKey : this.refKeys[e.index]
          } else {
            console.error(e)
          }
        })
    },
    request(formData) {
      let url = this.url.add,
        method = 'post'
      if (this.model.id) {
        url = this.url.edit
        method = 'post' //put修改
      }
      this.confirmLoading = true
      httpAction(url, formData, method)
        .then(res => {
          if (res.success) {
            this.$message.success(res.message)
            this.$emit('ok')
            this.close()
          } else {
            this.$message.warning(res.message)
          }
        })
        .finally(() => {
          this.confirmLoading = false
        })
    },
    handleSave() {
      console.log('这是暂存！')
    }
  }
}
</script>

<style lang="less" scoped>
.ant-form {
  padding-right: 48px;
}

.table-operator {
  button {
    margin-right: 5px;
    background-color: #6455ff;
    color: #fff;
    border: 1px solid #6455ff;
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
  .important {
    :global(.ant-checkbox-checked .ant-checkbox-inner) {
      background-color: #6455ff;
      border-color: #6455ff;
    }
    :global(&:hover .ant-checkbox-inner) {
      border-color: #6455ff;
    }
    :global(.ant-checkbox-input:focus + .ant-checkbox-inner) {
      border-color: #6455ff;
    }
  }
}
.btnClass {
  text-align: center;
  button {
    background-color: #6455ff;
    color: #fff;
  }
}
</style>