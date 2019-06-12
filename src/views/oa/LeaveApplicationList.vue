<template>
  <a-card :bordered="false">

    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="24">

          <!-- <a-col :md="6" :sm="8">
            <a-form-item label="请假人用户编码">
              <a-input placeholder="请输入请假人用户编码" v-model="queryParam.username"></a-input>
            </a-form-item>
          </a-col> -->
          <a-col :md="12" :sm="8">
            <a-form-item label="请假人姓名">
              <a-input placeholder="请输入请假人姓名" v-model="queryParam.realname"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="12" :sm="8">
            <a-form-item label="请假类型">
              <!-- <a-input placeholder="请输入请假类型" v-model="queryParam.type"></a-input> -->
              <a-select placeholder="请选择类型" v-model="queryParam.type">
                <a-select-option value="0">事假</a-select-option>
                <a-select-option value="1">病假</a-select-option>
                <a-select-option value="2">年假</a-select-option>
                <a-select-option value="3">婚假</a-select-option>
                <a-select-option value="4">出差</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
        <!-- <template v-if="toggleSearchStatus"> -->
          <a-col :md="6" :sm="8">
            <a-form-item label="填写的时间">
              <!-- <a-input placeholder="请输入请假开始时间" v-model="queryParam.timeStart"></a-input> -->
              <j-date v-model="queryParam.timeStart" :showTime="true" dateFormat="YYYY-MM-DD HH:mm:ss" :placeholder="'开始时间'"/>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="至">
              <!-- <a-input placeholder="请输入请假结束时间" v-model="queryParam.timeEnd"></a-input> -->
              <j-date v-model="queryParam.timeEnd" :showTime="true" dateFormat="YYYY-MM-DD HH:mm:ss" :placeholder="'结束时间'"/>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="状态">
              <!-- <a-input placeholder="请选择请假状态" v-model="queryParam.status"></a-input> -->
              <a-select placeholder="请选择状态" v-model="queryParam.status">
                <a-select-option value="0">暂存</a-select-option>
                <a-select-option value="1">流转中</a-select-option>
                <a-select-option value="2">已完成</a-select-option>
                <a-select-option value="3">废弃</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
        <!-- </template> -->
          <a-col :md="6" :sm="8" >
            <span style="float: right;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
              <!-- <a @click="handleToggleSearch" style="margin-left: 8px">
                {{ toggleSearchStatus ? '收起' : '展开' }}
                <a-icon :type="toggleSearchStatus ? 'up' : 'down'"/>
              </a> -->
            </span>
          </a-col>

        </a-row>
      </a-form>
    </div>

    <!-- 操作按钮区域 -->
    <div class="table-operator">
      <a-button @click="handleAdd" type="primary" icon="plus">启动</a-button>
      <!-- <a-button @click="handleEdit(record)" type="primary" icon="edit" :disabled="selectedRowKeys.length >0 && selectedRowKeys.length <=1? false : true">修改</a-button> -->
      <a-button @click="batchAbandone" type="primary" icon="delete" :disabled="selectedRowKeys.length <=0 || selectedRowKeys.length >1? true : false">废弃</a-button>
      <!-- <a-button type="primary" icon="download" @click="handleExportXls('请假申请')">导出</a-button>
      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">
        <a-button type="primary" icon="import">导入</a-button>
      </a-upload> -->
      <!-- <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>
      </a-dropdown> -->
      <a-button @click="searchReset" type="primary" icon="reload">刷新</a-button>
    </div>

    <!-- table区域-begin -->
    <div>
      <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">
        <i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择 <a style="font-weight: 600">{{ selectedRowKeys.length }}</a>项
        <a style="margin-left: 24px" @click="onClearSelected">清空</a>
      </div>

      <a-table
        ref="table"
        size="middle"
        bordered
        rowKey="id"
        key="id"
        :columns="columns"
        :dataSource="dataSource"
        :pagination="ipagination"
        :loading="loading"
        :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
        @change="handleTableChange">

        <span slot="action" slot-scope="text, record">
          <a @click="handleEdit(record)">编辑</a>

          <a-divider type="vertical" />
          <a @click="batchAbandone(record.id,record.processInstanceId)">删除</a>
          <!-- <a-dropdown>
            <a class="ant-dropdown-link">更多 <a-icon type="down" /></a>
            <a-menu slot="overlay">
              <a-menu-item>
                <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
                  <a>删除</a>
                </a-popconfirm>
              </a-menu-item>
            </a-menu>
          </a-dropdown> -->
        </span>

      </a-table>
    </div>
    <!-- table区域-end -->

    <!-- 表单区域 -->
    <leaveApplication-modal ref="modalForm" @ok="modalFormOk"></leaveApplication-modal>
  </a-card>
</template>

<script>
  import LeaveApplicationModal from './modules/LeaveApplicationModal'
  import { CmpListMixin } from '@/mixins/CmpListMixin'
  import JDate from '@/components/cmp/JDate'
  import JDictSelectTag from '../../components/dict/JDictSelectTag.vue'
  import { httpAction } from '@/api/manage'
  import qs from 'qs';

  export default {
    name: "LeaveApplicationList",
    mixins:[CmpListMixin],
    components: {
      LeaveApplicationModal,
      JDate,
      JDictSelectTag
    },
    data () {
      return {
        description: '请假申请管理页面',
        // 表头
        columns: [
          {
            title: '#',
            dataIndex: '',
            key:'rowIndex',
            width:60,
            align:"center",
            customRender:function (t,r,index) {
              return parseInt(index)+1;
            }
           },
		  //  {
      //       title: '请假人用户编码',
      //       align:"center",
      //       dataIndex: 'username'
      //      },
		   {
            title: '请假人',
            align:"center",
            dataIndex: 'inputerName'
           },
		   {
            title: '请假类型',
            align:"center",
            dataIndex: 'type',
            customRender: (text, record, index) => {
              let re = "";
              if (text === '0') {
                re = "事假";
              } else if (text === '1') {
                re = '病假';
              } else if (text === '2') {
                re = '年假';
              } else if (text === '3') {
                re = '婚假';
              } else if (text === '4') {
                re = '出差';
              } else {
                re = text;
              }
              return re;
            }
           },
		   {
            title: '请假开始时间',
            align:"center",
            dataIndex: 'timeStart'
           },
		   {
            title: '请假结束时间',
            align:"center",
            dataIndex: 'timeEnd'
           },
		   {
            title: '请假原因',
            align:"center",
            dataIndex: 'reason'
           },
		   {
            title: '部门领导',
            align:"center",
            dataIndex: 'departmentLeaderUsername'
           },
		  //  {
      //       title: '部门领导',
      //       align:"center",
      //       dataIndex: 'departmentLeaderRealname'
      //      },
		   {
            title: '人事部门领导',
            align:"center",
            dataIndex: 'hrLeaderUsername'
           },
		  //  {
      //       title: '人事部门领导',
      //       align:"center",
      //       dataIndex: 'hrLeaderRealname'
      //      },
		   {
            title: '总经理',
            align:"center",
            dataIndex: 'generalManagerUsername'
           },
		  //  {
      //       title: '总经理',
      //       align:"center",
      //       dataIndex: 'generalManagerRealname'
      //      },
		  //  {
      //       title: '流程定义ID',
      //       align:"center",
      //       dataIndex: 'processDefinitionId'
      //      },
		  //  {
      //       title: '流程实例ID',
      //       align:"center",
      //       dataIndex: 'processInstanceId'
      //      },
		   {
            title: '备注信息',
            align:"center",
            dataIndex: 'remarks'
           },
		   {
            title: '状态',
            align:"center",
            dataIndex: 'status',
            customRender: (t, r, index) => {
              let re = "";
              if (t === 0) {
                re = "暂存";
              } else if (t === 1) {
                re = '流转中';
              } else if (t === 2) {
                re = '已完成';
              } else if (t === 3) {
                re = '废弃';
              } else {
                re = t;
              }
              return re;
            }
           },
		  //  {
      //       title: '所属租户',
      //       align:"center",
      //       dataIndex: 'corpCode'
      //      },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            scopedSlots: { customRender: 'action' },
          }
        ],
		url: {
          list: "/oa/leaveApplication/list",
          // delete: "/oa/leaveApplication/delete",
          // deleteBatch: "/oa/leaveApplication/deleteBatch",
          // 废弃即删除
          // deleteBatch: "/oa/leaveApplication/action",
          deleteBatch: "/flowable/action",
          // delete: '/oa/leaveApplication/action',
          exportXlsUrl: "oa/leaveApplication/exportXls",
          importExcelUrl: "oa/leaveApplication/importExcel",
          list1: "/flowable/tasks/list",
          form: "/flowable/tasks/form"
       },
    }
  },
  computed: {
    importExcelUrl: function(){
      return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
    }
  },
    methods: {
      // 自定义废弃方法覆盖混合模式
      batchAbandone: function (id) {
        if(!this.url.deleteBatch){
          this.$message.error("请设置url.deleteBatch属性!")
          return
        }
          var ids = "";
          var processInsId= "";
          if(this.selectedRowKeys.length > 1){
            for (var a = 0; a < this.selectedRowKeys.length; a++) {
              ids += this.selectedRowKeys[a] + ",";
            }
            // processInsId = this.selectionRows[0].processInstanceId;
          } else if (this.selectedRowKeys.length = 1 && this.selectedRowKeys[0] !== undefined) {
            ids = this.selectedRowKeys[0];
            // processInsId = this.selectionRows[0].processInstanceId;
          } else {
            ids = id;
            // processInsId = processInstanceId
          }
          let flowDataString = {
            api: '/process/delete',
            // processInstanceId : processInsId
            processDefinitionKey: 'leave'
          }
          let formDataString = {
            id: ids
          }
          let params = {
            flowDataString: JSON.stringify(flowDataString),
            formDataString: JSON.stringify(formDataString),
          };
          let method = 'post';
          var that = this;
          this.$confirm({
            title: "确认废弃",
            content: "是否废弃选中数据?",
            onOk: function () {
              httpAction(that.url.deleteBatch, qs.stringify(params), method).then((res) => {
                if (res.success) {
                  that.$message.success(res.message);
                  that.loadData();
                  that.onClearSelected();
                } else {
                  that.$message.warning(res.message);
                }
              });
            }
          });
      }
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>