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
        bordered  
        rowKey="id"
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
    <!-- <task-modal ref="modalForm" @ok="modalFormOk"></task-modal> -->
    <leaveApplication-modal ref="modalForm" @ok="modalFormOk"></leaveApplication-modal>
    
  </a-card>
</template>

<script>
  // import TaskModal from './modules/TaskModal'
  import LeaveApplicationModal from '../oa/modules/LeaveApplicationModal'
  import { CmpListMixin } from '@/mixins/CmpListMixin'
  import { getAction } from '@/api/manage'

  export default {
    name: "TaskList",
    mixins:[CmpListMixin],
    components: {
      LeaveApplicationModal
    },
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
            dataIndex: 'startUserName'
          },
		      {
            title: '任务类型',
            align:"center",
            dataIndex: 'taskCategory'
          },
		      {
            title: '任务名称',
            align:"center",
            dataIndex: 'taskName'
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
      }
    },
    methods: {
      handleEditform: function (record) {
        let params = {
          businessKey: record.businessKey,
          formDataApi: record.formDataApi, 
          processDefinitionId: record.processDefinitionId, 
          processInstanceId: record.processInstanceId, 
          taskId: record.taskId
        }
        getAction(this.url.form, params).then((res) => {
          if (res.success) {
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
          if (res.success) {
            this.dataSource = res.result.dataList;
            this.ipagination.total = res.result.total;
          }
          this.loading = false;
        })
      }
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>