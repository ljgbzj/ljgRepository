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
                <a-select placeholder="选择" v-decorator="['stakeholderType', {initialValue: ['0']}]">
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
            <!-- <a-input placeholder v-decorator="['attachment', {}]" /> -->
            <a-upload
              :action="uploadAction"
              listType="picture"
              :headers="headers"
              :fileList="fileList"
              @change="handleChange"
              :multiple="true"
              :beforeUpload="beforeUpload"
              class="upload-list-inline"
              :data="uploadParams"
            >
              <a-button>
                <a-icon type="upload" />upload
              </a-button>
            </a-upload>
          </a-form-item>
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
                <a-input placeholder v-decorator="['inputerFullname', {}]" disabled />
              </a-form-item>
            </a-col>
            <a-col :span="12">
              <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="联系电话">
                <a-input placeholder v-decorator="['inputerPhoneNum', {}]" disabled />
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
                  disabled
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
            <!-- <a-input placeholder v-decorator="['subcontractor', {}]" /> -->
            <!-- <j-select-user-new :selectedDetails="departDetails" @userDetails="userDetails" class="userSelect"></j-select-user-new> -->
            <j-select-user-new :selectedDetails="departDetails" @userDetails="userDetails" />
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
import { httpAction, getAction } from '@/api/manage'
import JSelectUserNew from '@/components/cmpbiz/JSelectUserNew'
import { ACCESS_TOKEN } from '@/store/mutation-types'
import Vue from 'vue'
import md5 from 'md5'
import { mapGetters } from 'vuex'
import qs from 'qs';

export default {
  name: 'stakemodule',
  mixins: [JEditableTableMixin],
  components: {
    JDictSelectTag,
    JSelectUserNew
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
        fileUpload: window._CONFIG['domianURL'] + '/file/uploadFile/add',
        stakeholderDetail: {
          list: '/stakeholder/stakeholder/queryById'
        }
      },
      headers: {},
      fileList: [],
      attachment: [
        {
          groupId: '',
          fileTokens: '',
          fieldName: 'attachment',
          tableName: 'pm_stakeholder'
        }
      ],
      uploadParams: {},
      curtime: '',
      departDetails: [],
      subcontractorName: [],
      subcontractorFullname: []
    }
  },
  created() {
    const token = Vue.ls.get(ACCESS_TOKEN)
    this.headers = { 'X-Access-Token': token }
    this.curtime = new Date()
    this.attachment[0].fileTokens = ''
  },
  computed: {
    uploadAction() {
      return this.url.fileUpload
    }
  },
  methods: {
    ...mapGetters(['nickname', 'userInfo']),
    /** 调用完edit()方法之后会自动调用此方法 */
    editAfter(record) {
      this.$nextTick(() => {
        this.form.setFieldsValue(
          pick(
            this.model,
            'stakeholderType',
            'companyCode',
            'companyName',
            /* 'sourceType', */
            'companyNature',
            'phoneNum',
            'fax',
            'postCode',
            'address',
            'website',
            'openingBank',
            'accountNumber',
            'remarks',
            /* 'subcontractorName',
            'subcontractorFullname', */
            /* 'inputerName', */
            'inputerFullname',
            'inputerPhoneNum',
            /* 'inputerDeptCode', */
            'inputerDeptName',
            /* 'attachment', */
            'notifyMethod',
            /* 'status', */
            /* 'corpCode',
            'prjCode' */
          )
        )
        

        this.departDetails = []
        if (JSON.stringify(record) !== '{}') {
          this.departDetails = this.initSelect([this.model.subcontractorName, this.model.subcontractorFullname])
        }

        console.log(this.form,'form')
        console.log(this.model,'model')
        this.form.setFieldsValue({ inputerFullname: this.nickname() })
        console.log(this.userInfo())
        this.form.setFieldsValue({ inputerPhoneNum: this.userInfo().phone })

        // 时间格式化
        this.form.setFieldsValue({
          inputerTime: this.model.inputerTime ? moment(this.model.inputerTime) : moment(this.curtime)
        })
      })
      //  初始化上传文件
      if (this.model.attachment !== undefined) {
        console.log(this.model.attachment, '拿到的attachment')
        this.attachment[0].groupId = this.model.attachment
        this.attachment[0].fileTokens = ''
        getAction(this.url.fileFind, { groupId: this.model.attachment }).then(res => {
          console.log(res, '请求到的attachment')
          for (let i = 0; i < res.result.length; i++) {
            if (
              res.result[i].viewPath.substring(res.result[i].viewPath.length - 4) == 'jpeg' ||
              res.result[i].viewPath.substring(res.result[i].viewPath.length - 3) == 'jpg' ||
              res.result[i].viewPath.substring(res.result[i].viewPath.length - 3) == 'png'
            ) {
              console.log('第一个进来了')
              let fileChild = {
                response: {
                  result: {
                    fileTokens: res.result[i].fileToken
                  }
                },
                uid: i,
                name: res.result[i].uploadFile.fileName,
                status: 'done',
                type: 'image/jpeg',
                url: this.url.imgerver + '/' + res.result[i].viewPath
              }
              this.fileList.push(fileChild)
              this.attachment[0].fileTokens += res.result[i].fileToken + ','
            } else {
              console.log('第二个进来了')
              let fileChild = {
                response: {
                  result: {
                    fileTokens: res.result[i].fileToken
                  }
                },
                uid: i,
                name: res.result[i].uploadFile.fileName,
                status: 'done',
                type: 'text/plain',
                url: this.url.imgerver + '/' + res.result[i].viewPath
              }
              this.fileList.push(fileChild)
              this.attachment[0].fileTokens += res.result[i].fileToken + ','
            }
          }
        })
        console.log(this.attachment, '合成问题')
      } else {
        this.attachment[0].groupId = this.uuid()
        this.attachment[0].fileTokens = ''
      }
      console.log(this.attachment[0].groupId, '一开始的groupId')
      console.log(this.model,'model')
      // 加载子表数据

      if (this.model.id) {
        let params = { id: this.model.id }
        console.log(params,'params')
        this.requestSubTableData(this.url.stakeholderDetail.list, params, this.stakeholderDetailTable)
      }
    },

    initSelect(val) {   
      var arr2=[];
      if (val[0].indexOf(",") !== -1) {
        let arr = val[0].split(",");
        let arr1 = val[1].split(",");
        for (let i = 0; i<arr.length; i++) {
          arr2.push({
            "realname": arr[i],
            "username": arr1[i]
          })
        }
      } else {
        arr2 = [{
          "realname": val[0],
          "username": val[1]
        }]
      }
      return arr2;
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
    // 新增提交
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
          formData.subcontractorName = this.subcontractorName
          formData.subcontractorFullname = this.subcontractorFullname

          // important值true或false转换为1或0
          let len = formData.stakeholderDetailList.length
          for (let i = 0; i < len; i++) {
            formData.stakeholderDetailList[i].important = formData.stakeholderDetailList[i].important ? 1 : 0
          }

          // 发起请求
          /* console.log(allValues, 'allValues')
          console.log(formData, 'formData') */
          let data = {}
          let flowDataString = Object.assign(
            {},
            {
              api: '/process/startAndSubmit',
              processDefinitionKey: 'stakeholder'
            }
          )
          let formDataString = Object.assign({}, formData)
          let attachmentString = this.attachment
          // 手机号码和电话号码转换为数字
          formDataString.inputerPhoneNum = Number(formDataString.inputerPhoneNum) //录入人电话
          formDataString.phoneNum = Number(formDataString.phoneNum) //电话号码
          // 提醒方式数组改为字符串
          formDataString.notifyMethod = this.transformNotice(formDataString.notifyMethod)

          
          formDataString.stakeholderDetailList.forEach(function(item,index){
            item.phoneNum = Number(item.phoneNum)
            item.telephoneNum = Number(item.telephoneNum)
            item.id = null
          })

          console.log(formDataString.stakeholderDetailList)
          data.flowDataString = JSON.stringify(flowDataString)
          data.formDataString = JSON.stringify(formDataString)
          data.attachmentString = JSON.stringify(attachmentString)
          console.log(flowDataString,'flowDataString')
          console.log(formDataString,'formDataString')
          console.log(attachmentString,'attachmentString')
          console.log(data, 'data')
          data = qs.stringify(data)
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
      /* console.log(this.model.id)
      let url = this.url.add,
        method = 'post' */
      
      let url = this.url.edit
      let method = 'post' //put修改
      
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
    },
    // 上传文件改变时的状态，根据返回值中file的status，作出相应提示
    handleChange(info) {
      console.log(info, 'info', info.file.status)
      this.uploadParams = {}
      if (info.file.status === 'removed') {
        console.log(info, '移除返回')
        console.log(this.attachment[0].fileTokens, '母亲')
        console.log(info.file.response.result.fileTokens, 'jialuo')
        this.attachment[0].fileTokens = this.attachment[0].fileTokens.replace(
          info.file.response.result.fileTokens + ',',
          ''
        )
        console.log(this.attachment, '删除后的superstar')
      }
      this.fileList = info.fileList
      if (info.file.status === 'uploading') {
        return
      }
      if (info.file.status === 'done') {
        var response = info.file.response
        if (response.success) {
          this.picUrl += response.message + ','
          console.log(response.result, '返回值')
          this.attachment[0].fileTokens += response.result.fileTokens + ','
          console.log(this.attachment[0].fileTokens, '看看之后')
          // this.attachment.push({
          //   name: response.result.uploadFile.fileName,
          //   url: response.result.viewPath,
          //   id: response.result.fileTokens
          // })
        } else {
          this.$message.warning(response.message)
        }
      }
    },
    // 上传文件前的钩子函数
    beforeUpload(file, fileList) {
      this.uploadParams = {
        json: '{"md5":"' + md5(JSON.stringify(file)) + '","fileName":"' + file.name + '"}'
      }
    },
    userDetails(val) {
      this.subcontractorName = val.realname
      this.subcontractorFullname = val.username
    },
    uuid() {
      var s = []
      var hexDigits = '0123456789abcdef'
      for (var i = 0; i < 36; i++) {
        s[i] = hexDigits.substr(Math.floor(Math.random() * 0x10), 1)
      }
      s[14] = '4' // bits 12-15 of the time_hi_and_version field to 0010
      s[19] = hexDigits.substr((s[19] & 0x3) | 0x8, 1) // bits 6-7 of the clock_seq_hi_and_reserved to 01
      // s[8] = s[13] = s[18] = s[23] = "-";
      s[8] = s[13] = s[18] = s[23] = ''
      var uuid = s.join('')
      return uuid
    },
    transformNotice(arr){
      let len = arr.length
      if(len == 1){
        return '100'
      }
      else if(len == 3){
        return '111'
      }
      else if(len == 2){
        if(arr[1] == '1'){
          return '110'
        }else{
          return '101'
        }
      }
    },
    transformStakeHL(arr){
      function trans(item, index){
        console.log(item,index)
        item.phoneNum = Number(item.phoneNum)
        item.telephoneNum = Number(item.telephoneNum)
      }
      return arr.forEach(trans)
    }
  }
}
</script>

<style lang="less" scoped>
@import '~@/assets/less/modal.less';

.ant-form {
  /* padding-right: 48px; */
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