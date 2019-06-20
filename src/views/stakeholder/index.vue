<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="24">
          <a-col :md="11" :sm="8">
            <a-form-item label="干系人员类型">
              <a-select placeholder="选择" v-model="queryParam.type">
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
              <a-input placeholder="请输入企业/个人名称" v-model="queryParam.realname"></a-input>
            </a-form-item>
          </a-col>
          <a-col>
            <span style="float: right;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
            </span>
          </a-col>
          <a-col :md="11" :sm="8">
            <a-form-item label="信息来源方式">
              <a-select placeholder="请选择信息来源方式" v-model="queryParam.way">
                <a-select-option value="0">项目新增/企业选入</a-select-option>
                <a-select-option value="1">信息来源方式2</a-select-option>
                <a-select-option value="2">信息来源方式3</a-select-option>
                <a-select-option value="3">信息来源方式4</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :md="11" :sm="8">
            <a-form-item label="企业/单位性质">
              <a-input placeholder="请输入企业/单位性质" v-model="queryParam.kind"></a-input>
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
      <a-button @click="handleChose" type="primary" icon="plus">选入</a-button>
      <a-button @click="handleAdd" type="primary" icon="plus">添加</a-button>
      <a-button @click="handleRevise" type="primary" icon="plus">修改</a-button>
      <a-button @click="handleDelete" type="primary" icon="delete">删除</a-button>
      <a-button @click="handleScan" type="primary" icon="plus">浏览</a-button>
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
        rowKey="key"
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
import JDate from '@/components/cmp/JDate'
import JDictSelectTag from '../../components/dict/JDictSelectTag.vue'
import { httpAction } from '@/api/manage'
import qs from 'qs'

export default {
  name: 'stakeholder',
  mixins: [CmpListMixin],

  components: {
    stakemodule,
    JDate,
    JDictSelectTag
  },
  data() {
    return {
      description: '请假申请管理页面',
      // 表头
      columns: [
        {
          title: '序号',
          dataIndex: 'key',
          key: 'key',
          width: 60,
          align: 'center'
        },
        {
          title: '干系人类型',
          align: 'center',
          dataIndex: 'type'
        },
        {
          title: '企业/个人名称',
          align: 'center',
          dataIndex: 'realname'
        },
        {
          title: '企业/单位性质',
          align: 'center',
          dataIndex: 'kind'
        },
        {
          title: '干系人信息来源方式',
          align: 'center',
          dataIndex: 'way'
        }
      ],
      dataSource: [
        {
            key: '1',
            realname: '尼古拉斯·赵四',
            type: '干系人类型1',
            kind: '国企',
            way: '项目新增/企业选入'
        }, 
        {
            key: '2',
            realname: '郭德纲',
            type: '干系人类型2',
            kind: '私企',
            way: '信息来源方式2'
        }, 
        {
            key: '3',
            realname: 'CXK',
            type: '干系人类型3',
            kind: '央企',
            way: '信息来源方式3'
        }, 
        {
            key: '4',
            realname: '迪丽热巴',
            type: '干系人类型4',
            kind: '民企',
            way: '信息来源方式4'
        }, 
      ],
      url: {
          list: ''
      }
    }
  },
  computed: {},
  methods: {
    handleChose() {
      console.log('这是选入方法！')
    },
    /* handleAdd() {
      console.log('这是添加方法！')
    }, */
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
</style>