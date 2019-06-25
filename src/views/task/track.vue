<template>
  <a-card :bordered="false">

    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="24">

          <a-col :md="6" :sm="8">
            <a-form-item label="发起人">
              <a-input placeholder="请输入发起人" v-model="queryParam.inputerFullname"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="任务类型">
              <a-input placeholder="请输入任务类型" v-model="queryParam.taskCategory"></a-input>
            </a-form-item>
          </a-col>

          <template v-if="toggleSearchStatus">
            <a-col :md="6" :sm="8">
              <a-form-item label="任务名称">
                <a-input placeholder="请输入任务名称" v-model="queryParam.taskName"></a-input>
              </a-form-item>
            </a-col>
            <a-col :md="6" :sm="8">
              <a-form-item label="环节名称">
                <a-input placeholder="请输入环节名称" v-model="queryParam.nodeName"></a-input>
              </a-form-item>
            </a-col>
            <a-col :md="6" :sm="8">
              <a-form-item label="开始时间">
                <a-input placeholder="请输入开始时间" v-model="queryParam.startTime"></a-input>
              </a-form-item>
            </a-col>
          </template>
          <a-col :md="6" :sm="8" >
            <span style="float: left; overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
              <a @click="handleToggleSearch" style="margin-left: 8px">
                {{ toggleSearchStatus ? '收起' : '精确查询' }}
                <a-icon :type="toggleSearchStatus ? 'up' : 'down'"/>
              </a>
            </span>
          </a-col>

        </a-row>
      </a-form>
    </div>

    
    <!-- table区域-begin -->
    <div>

      <a-table
        ref="table"
        size="middle"
        :rowKey="rowkey"
        :columns="columns"
        :dataSource="dataSource"
        :pagination="ipagination"
        :loading="loading"
        @change="handleTableChange">
        <span slot="action" slot-scope="text, record">
          <a @click="handleEditform(record)">查看</a>&nbsp;
          <a @click="handleEdit(record)">委托</a>
        </span>
      </a-table>

    </div>
    <!-- table区域-end -->

    <!-- 表单区域 -->
    <component :is="comName" ref="modalForm" @ok="modalFormOk"></component>  
  </a-card>
</template>

<script>
  // import TaskModal from './modules/TaskModal'
  import { CmpListMixin } from '@/mixins/CmpListMixin'
  import { getAction } from '@/api/manage'

  export default {
    name: "TaskList",
    mixins:[CmpListMixin],
    data () {
      return {
        description: '待办任务管理页面',
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
            title: '发起人',
            align:"center",
            dataIndex: 'startUserFullname'
          },
		      {
            title: '任务类型',
            align:"center",
            dataIndex: 'taskCategory'
          },
		      {
            title: '任务名称',
            align:"center",
            dataIndex: 'taskSubject'
          },
		      {
            title: '环节名称',
            align:"center",
            dataIndex: 'nodeName'
          },
		      {
            title: '开始时间',
            align:"center",
            dataIndex: 'startTime'
          },
		      {
            title: '执行人',
            align:"center",
            dataIndex: 'assigneeName'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            scopedSlots: { customRender: 'action' },
          }
        ],
		    url: {
          list: "/flowable/process/list",
          form: "/flowable/tasks/form"
        },
        rowkey: '',
        componentsUrl: 'oa/modules/LeaveApplicationModal'
      }
    },
    methods: {
      handleEditform: function (record) {
        console.log(record,'追踪');
        let params = {
          businessKey: record.businessKey,
          processDefinitionKey: record.processDefinitionKey, 
          processDefinitionId: record.processDefinitionId, 
          processInstanceId: record.processInstanceId, 
          taskId: record.taskId
        }
        getAction(this.url.form, params).then((res) => {
          if (res.success) {
            this.componentsUrl = res.result.formPath;
            record = Object.assign(res.result, {taskId: record.taskId}) 
            //this.dataSource = res.result.dataList;
            //this.ipagination.total = res.result.total;
            this.$refs.modalForm.edit(record);
            this.$refs.modalForm.title = "查看";
          }
        })
      },
      loadData(arg) {
        if(!this.url.list){
          this.$message.error("请设置url.list属性!")
          return
        }
        //加载数据 若传入参数1则加载第一页的内容
        if (arg === 1) {
          this.ipagination.current = 1;
        }
        var params = this.getQueryParams();//查询条件
        this.loading = true;
        getAction(this.url.list, params).then((res) => {
          console.log(res,'不是');
          if (res.success) {
            this.dataSource = res.result.dataList;
            this.ipagination.total = res.result.total;
          }
          this.loading = false;
        })
      }
    },
    computed: {
      comName: function () {
        return () => import(`@/views/${this.componentsUrl}.vue`)
      }
    }
  }
</script>
<style lang="less" scoped>
  @import '~@assets/less/common.less';
  // 组件内直接引入ant组件样式覆盖
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
</style>