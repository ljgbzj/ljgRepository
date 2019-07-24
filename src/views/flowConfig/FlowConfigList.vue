<template>
  <a-card :bordered="false">

    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="24">

          <a-col :md="6" :sm="8">
            <a-form-item label="流程定义key">
              <a-input placeholder="请输入流程定义key" v-model="queryParam.processDefinitionKey"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="流程名称">
              <a-input placeholder="请输入流程名称" v-model="queryParam.processName"></a-input>
            </a-form-item>
          </a-col>
        <template v-if="toggleSearchStatus">
        <a-col :md="6" :sm="8">
            <a-form-item label="表单名称">
              <a-input placeholder="请输入表单名称" v-model="queryParam.formName"></a-input>
            </a-form-item>
          </a-col>
          </template>
        <template v-if="toggleSearchStatus">
          <a-col :md="6" :sm="8">
            <a-form-item label="功能是否弃用">
              <a-input placeholder="请输入功能是否弃用" v-model="queryParam.status"></a-input>
            </a-form-item>
          </a-col>
        </template>

          <a-col :md="6" :sm="8" >
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
              <a @click="handleToggleSearch" style="margin-left: 8px">
                {{ toggleSearchStatus ? '收起' : '展开' }}
                <a-icon :type="toggleSearchStatus ? 'up' : 'down'"/>
              </a>
            </span>
          </a-col>

        </a-row>
      </a-form>
    </div>

    <!-- 操作按钮区域 -->
    <div class="table-operator">
      <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>
      <a-button type="primary" icon="download" @click="handleExportXls('流程表单配置')">导出</a-button>
      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">
        <a-button type="primary" icon="import">导入</a-button>
      </a-upload>
      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>
      </a-dropdown>
      <div class="ant-alert ant-alert-info">
        <i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择 <a style="font-weight: 600">{{ selectedRowKeys.length }}</a>项
        <a style="margin-left: 24px" @click="onClearSelected">清空</a>
      </div>
    </div>

    <!-- table区域-begin -->
    <div>
      <a-table
        ref="table"
        size="middle"
        rowKey="id"
        :columns="columns"
        :dataSource="dataSource"
        :pagination="ipagination"
        :loading="loading"
        :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
        @change="handleTableChange">

        <span slot="action" slot-scope="text, record">
          <a @click="handleEdit(record)">编辑</a>

          <a-divider type="vertical" />
          <a @click="handleDelete(record.id)">删除</a>
        </span>

      </a-table>
    </div>
    <!-- table区域-end -->

    <!-- 表单区域 -->
    <flowConfig-modal ref="modalForm" @ok="modalFormOk"></flowConfig-modal>
  </a-card>
</template>

<script>
  import FlowConfigModal from './modules/FlowConfigModal'
  import { CmpListMixin } from '@/mixins/CmpListMixin'
  import {initDictOptions, filterDictText} from '@/components/dict/JDictSelectUtil'
  import { deleteAction } from '@/api/manage'

  export default {
    name: "FlowConfigList",
    mixins:[CmpListMixin],
    components: {
      FlowConfigModal
    },
    data () {
      return {
        description: '流程表单配置管理页面',
        // 表头
        columns: [
          {
            title: '序号',
            dataIndex: '',
            key:'rowIndex',
            width:60,
            align:"center",
            customRender:function (t,r,index) {
              return parseInt(index)+1;
            }
           },
           {
             title: '流程定义key',
             align:"center",
             dataIndex: 'processDefinitionKey'
           },
           {
             title: '流程名称',
             align:"center",
             dataIndex: 'processName'
           },
           {
             title: '表单名称',
             align:"center",
             dataIndex: 'formName'
           },
           {
             title: '描述',
             align:"center",
             dataIndex: 'description'
           },
           {
             title: '功能是否弃用（0正常 1弃用）',
             align:"center",
             dataIndex: 'status',
             customRender: (text, record, index) => {
               //字典值替换通用方法
               return filterDictText(this.statusDictOptions, text);
             }
           },
           {
             title: '上级菜单名称',
             align:"center",
             dataIndex: 'parentMenuName'
           },
           {
             title: '菜单名称',
             align:"center",
             dataIndex: 'menuName'
           },
           {
             title: '前端路由',
             align:"center",
             dataIndex: 'router'
           },
           {
             title: '前端表单所在路径',
             align:"center",
             dataIndex: 'formPath'
           },
           {
             title: '后端package所在路径(如:oa/leaveApplication）',
             align:"center",
             dataIndex: 'packagePath'
           },
           {
             title: '实体类名（首字母改为小写字母）',
             align:"center",
             dataIndex: 'entityName'
           },
           {
             title: '表单数据表名',
             align:"center",
             dataIndex: 'formTableName'
           },
           {
             title: '表单所在数据库名',
             align:"center",
             dataIndex: 'formDbName'
           },
           {
             title: '维护人员用户名',
             align:"center",
             dataIndex: 'maintainUserName'
           },
           {
             title: '维护人员姓名',
             align:"center",
             dataIndex: 'maintainUserFullname'
           },
           {
             title: '维护人员联系电话',
             align:"center",
             dataIndex: 'maintainUserPhoneNum'
           },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            scopedSlots: { customRender: 'action' },
          }
        ],
		url: {
          list: "/sysFlow/flowConfig/list",
          delete: "/sysFlow/flowConfig/delete",
          deleteBatch: "/sysFlow/flowConfig/deleteBatch",
          exportXlsUrl: "sysFlow/flowConfig/exportXls",
          importExcelUrl: "sysFlow/flowConfig/importExcel",
       },
    }
  },
  computed: {
    importExcelUrl: function(){
      return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
    }
  },
  beforeCreate(){
      // 自行定义方法，引用字典
      //初始化字典
      initDictOptions('bpm_status').then((res) => {
        if (res.success) {
          this.statusDictOptions = res.result;
        }
      });
      initDictOptions('leave_type').then((res) => {
        if (res.success) {
          this.leaveDictOptions = res.result;
        }
      });
    },
    methods: {
       handleDelete: function (id) {
               if(!this.url.delete){
                 this.$message.error("请设置url.delete属性!")
                 return
               }
               var that = this;
               this.$confirm({
                 title: "确认删除",
                 content: "是否删除选中数据?",
                 onOk: function () {
                   deleteAction(that.url.delete, {id: id}).then((res) => {
                     if (res.success) {
                       that.$message.success(res.message);
                       that.loadData();
                     } else {
                       that.$message.warning(res.message);
                     }
                   });
                 }
               });
             },
    }
  }
</script>
<style lang="less" scoped>
  @import '~@assets/less/common.less';
  @import '~@assets/less/topBtns.less';
</style>