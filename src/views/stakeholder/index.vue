<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="24">
          <a-col :md="11" :sm="8">
            <a-form-item label="干系人类型">
              <a-select placeholder="选择" v-model="queryParam.stakeholderType">
                <a-select-option value="0">干系人类型1</a-select-option>
                <a-select-option value="1">干系人类型2</a-select-option>
                <a-select-option value="2">干系人类型3</a-select-option>
                <a-select-option value="3">干系人类型4</a-select-option>
                <a-select-option value="4">干系人类型5</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :md="11" :sm="8">
            <a-form-item label="企业/个人名称">
              <a-input placeholder="请输入企业/个人名称" v-model="queryParam.companyName"></a-input>
            </a-form-item>
          </a-col>
          <a-col>
            <span style="float: right;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
            </span>
          </a-col>
          <a-col :md="11" :sm="8">
            <a-form-item label="信息来源方式">
              <a-select placeholder="请选择信息来源方式" v-model="queryParam.companyName">
                <a-select-option value="0">项目新增/企业选入</a-select-option>
                <a-select-option value="1">信息来源方式2</a-select-option>
                <a-select-option value="2">信息来源方式3</a-select-option>
                <a-select-option value="3">信息来源方式4</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :md="11" :sm="8">
            <a-form-item label="企业/单位性质">
              <a-input placeholder="请输入企业/单位性质" v-model="queryParam.companyNature"></a-input>
            </a-form-item>
          </a-col>
          <!-- </template> -->
          <a-col>
            <span style="float: right;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchReset" icon="reload">清空</a-button>
            </span>
          </a-col>
        </a-row>
      </a-form>
    </div>

    <!-- 操作按钮区域 -->
    <div class="table-operator">
      <a-button @click="handleAdd" type="primary" icon="plus">启动</a-button>
      <a-button @click="handleRevise" type="primary" icon="edit">编辑</a-button>
      <a-button @click="batchDel" type="primary" icon="delete">删除</a-button>
      <a-button @click="handleScan" type="primary" icon="eye">浏览</a-button>
      <a-button @click="searchReset" type="primary" icon="reload">刷新</a-button>
    </div>

    <!-- table区域-begin -->
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
        rowKey="id"
        :columns="columns"
        :dataSource="dataSource"
        :pagination="ipagination"
        :loading="loading"
        :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
        @change="handleTableChange"
      ></a-table>
    </div>
    <!-- table区域-end -->

    <!-- 表单区域 -->
    <stakemodule ref="modalForm" @ok="modalFormOk"></stakemodule>
  </a-card>
</template>

<script>
import stakemodule from './modules/stakemodule'
import { CmpListMixin } from '@/mixins/CmpListMixin'

export default {
  name: 'stakeholder',
  mixins: [CmpListMixin],

  components: {
    stakemodule
  },
  data() {
    return {
      description: '请假申请管理页面',
      // 表头
      columns: [
        {
          title: '序号',
          dataIndex: '',
          key: 'rowIndex',
          width: 60,
          align: 'center',
          customRender: function(t, r, index) {
            return parseInt(index) + 1
          }
        },
        {
          title: '干系人类型',
          align: 'center',
          dataIndex: 'stakeholderType'
        },
        {
          title: '企业/个人名称',
          align: 'center',
          dataIndex: 'companyName'
        },
        {
          title: '企业/单位性质',
          align: 'center',
          dataIndex: 'companyNature'
        },
        {
          title: '干系人信息来源方式',
          align: 'center',
          dataIndex: 'sourceType'
        }
      ],
      url: {
        list: '/stakeholder/stakeholder/list',
        delete: '/stakeholder/stakeholder/delete',
        deleteBatch: '/stakeholder/stakeholder/deleteBatch',
        exportXlsUrl: 'stakeholder/stakeholder/exportXls',
        importExcelUrl: 'stakeholder/stakeholder/importExcel'
      }
    }
  },
  computed: {
    importExcelUrl: function() {
      return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`
    }
  },
  methods: {
    handleChose() {
      console.log('这是选入方法！')
    },
    handleRevise() {
      console.log('这是修改方法！')
    },
    handleDelete() {
      console.log('这是删除方法！')
    },
    handleScan() {
      console.log('这是浏览方法！')
    },
    handleReset() {
      console.log('这是刷新方法！')
    }
  }
}
</script>

<style lang="less" scoped>
@import '~@assets/less/common.less';

/* 组件内直接引入ant组件样式覆盖 */
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
  height: 40px;
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

.ant-card-body .table-operator > div {
  flex: 1;
  margin-left: 14px;
}

.ant-card-body .table-operator .ant-alert-info {
  border: unset;
  border-radius: 4px;
  background: rgba(109, 98, 255, 0.1);
}

//时间选择
.ant-calendar-picker {
  width: 100% !important;
  :global(.ant-input) {
    height: 40px;
  }
}
</style>