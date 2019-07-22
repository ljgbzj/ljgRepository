<template>
  <a-modal
    :title="null"
    :footer="null"
    :closable="false"
    :width="1000"
    :visible="visible"
    @ok="handleOk"
    @cancel="handleCancel"
    cancelText="关闭">
    <div class="title">
      <div>
        <img src="@/assets/img/login/selectUser.png" />
        选择用户
      </div>
      <a-icon type="close" class="closeIcon" @click="handleCancel"/>
    </div>
    <a-row :gutter="13">
      <a-col :span="13">
        <a-tree-select
          style="width: 100%;margin-bottom: 10px;"
          :dropdownStyle="{ maxHeight: '200px', overflow: 'auto' }"
          :treeData="departTree"
          placeholder='请选择部门'
          @select="this.onSelectDepart"
          treeDefaultExpandAll
          v-model="value"
          allowClear
        >
          <span style="color: #08c" slot="title" slot-scope="{key, value}" v-if="key='0-0-1'">
            Child Node1 {{value}}
          </span>
        </a-tree-select>
        <a-input-search
          placeholder="输入账号或姓名搜索"
          style="width: 100%;margin-bottom:10px;"
          @search="searchQuery"
          v-model="queryParam.dimName"
        />
        <!-- <a-card :bordered="true"> -->
          <!-- table区域-begin -->
            <a-tabs type="card">
              <a-tab-pane tab="用户列表" key="1">
                <div style="border:1px solid #e8e8e8;height:360px;">
                  <a-table
                    size="middle"
                    rowKey="id"
                    :columns="columns1"
                    :dataSource="dataSource1"
                    :pagination="ipagination"
                    :loading="loading"
                    :scroll="{ y: 250 }"
                    style="border-top:unset;"
                    :rowSelection="{selectedRowKeys: selectedRowKeys,onSelectAll:onSelectAll,onSelect:onSelect,onChange: onSelectChange}"
                    @change="handleTableChange">
                  </a-table>
                </div>
              </a-tab-pane>
              <a-tab-pane tab="最近添加" key="2">
                <div style="border:1px solid #e8e8e8;height:360px;">
                  <a-table               
                    size="middle"
                    rowKey="id"
                    :columns="columns1"
                    :dataSource="dataSource3"
                    :pagination="ipagination3"
                    :loading="loading"
                    :scroll="{ y: 250 }"
                    style="border-top:unset;"
                    :rowSelection="{selectedRowKeys: selectedRowKeys,onSelectAll:onSelectAll,onSelect:onSelect,onChange: onSelectChange}"
                    @change="handleTableChange3">
                  </a-table>
                </div>
              </a-tab-pane>
            </a-tabs>
          <!-- table区域-end -->
        <!-- </a-card> -->
      </a-col> 
      <a-col :span="1">
        <a-icon type="right" class="selectUserIcon" style="margin-top:200px;width:30px;height:30px;line-height:32px;text-algin:center;border-radius:50%;background: rgba(109,98,255,0.1)"/>
      </a-col>
      <a-col :span="10">
        <!-- <a-card title="用户选择" :bordered="true"> -->
          <!-- table区域-begin -->
          <div>
            <div style="margin-bottom:10px;height:30px;">
              <span>已选用户</span>
              <a-button style="float:right;right:0;top:0;" @click="clearSelected">清除</a-button>
            </div>
            <div style="border:1px solid #e8e8e8;height:440px;">
              <a-table
                size="middle"
                rowKey="id"
                :columns="columns2"
                :dataSource="dataSource2"
                :loading="loading"
                :scroll="{ y: 400 }"
                class="selectedTable"
              >
                <span slot="action" slot-scope="text, record">
                  <a @click="handleDelete(record)">删除</a>
                  <!-- <a-button type="primary" size="small" @click="handleDelete(record)" icon="delete">删除</a-button> -->
                </span>
              </a-table>
            </div>
          </div>
          <!-- table区域-end -->
        <!-- </a-card> -->
      </a-col>
    </a-row>
    <a-row :gutter="24">
        <div class="btnClass">
          <div>
            <span>共选择</span>
            <span>&nbsp;{{ selectLng }}&nbsp;</span>
            <span>人</span>
          </div>
          <a-button @click="handleCancel" icon="close" style="margin-right:10px" class="cancel">关闭</a-button>
          <a-button @click="handleOk" icon="check" type="primary" style="margin-right:10px" class="confirm">确定</a-button>
        </div>
    </a-row>
  </a-modal>
</template>

<script>
  import { filterObj } from '@/utils/util'
  import { getAction } from '@/api/manage'
  import { queryDepartTreeList, getUserList, queryUserByDepId, getLastlatestTwoList } from '@/api/api'

  export default {
    name: 'JSelectUserNewModal',
    data () {
      return {
        title: "用户列表",
        names: [],
        visible: false,
        placement: 'right',
        description: '人员管理页面',
        selectedKeys: [],
        departTree: [],
        // 查询条件
        queryParam: {},
        // 表头
        columns1: [
          {
            title: '账号',
            align:"center",
            width: 100,
            dataIndex: 'username'
          },
          {
            title: '姓名',
            align:"center",
            width: 100,
            dataIndex: 'realname'
          },
          {
            title: '所属部门',
            align:"center",
            width: 100,
            dataIndex: 'departName'
          }
        ],
        columns2: [
          {
            title: '账号',
            align: "center",
            dataIndex: 'username'
          },
          {
            title: '姓名',
            align:"center",
            width: 80,
            dataIndex: 'realname'
          },
          {
            title: '所属部门',
            align:"center",
            width: 80,
            dataIndex: 'departName'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            width: 100,
            scopedSlots: { customRender: 'action' },
          }
        ],
        //数据集
        dataSource1:[],
        dataSource2:[],
        dataSource3:[],
        // 分页参数
        ipagination:{
          current: 1,
          pageSize: 10,
          pageSizeOptions: ['10', '20', '30'],
          showTotal: (total, range) => {
            console.log(total,range,'想想就扯犊子');
            return range[0] + "-" + range[1] + " 共" + total + "条"
          },
          showQuickJumper: true,
          showSizeChanger: true,
          total: 0
        },
        ipagination3:{
          current: 1,
          pageSize: 10,
          pageSizeOptions: ['10', '20', '30'],
          showTotal: (total, range) => {
            return range[0] + "-" + range[1] + " 共" + total + "条"
          },
          showQuickJumper: true,
          showSizeChanger: true,
          total: 0
        },
        isorter:{
          column: 'createTime',
          order: 'desc',
        },
        loading:false,
        selectedRowKeys: [],
        selectedRows: [],
        url: {
          list: "/test/cmpDemo/list",
          UserList: "/sys/user/batchQueryByUsernames"
        },
        value: undefined
      }
    },
    props: {
      selectListUser: {
        type: Array,
        required:false
      },
    },
    created() {
      this.loadData();
      // 加载吧部门树
      this.queryDepartTree();
      // 请求最近添加列表
      this.latestTwoSelected();
    },
    methods: {
      searchQuery(){
        this.loadData(1);
      },
      searchReset(){
        this.queryParam={};
        this.loadData(1);
      },
      handleCancel() {
        this.visible = false;
        this.selectedRowKeys = [];
        this.selectionRows = [];
      },
      handleOk() {
        this.$emit("selectFinished",this.dataSource2);
        this.visible = false;
        this.selectedRowKeys = [];
        this.selectionRows = [];
      },
      add() {
        this.visible = true;
      },
      loadData (arg){
        //加载数据 若传入参数1则加载第一页的内容
        if(arg===1){
          this.ipagination.current = 1;
        }
        var params = this.getQueryParams(this.ipagination);//查询条件
        // getAction(this.url.list,params).then((res)=>{
        //   if(res.success){
        //     this.dataSource1 = res.result.records;
        //     this.ipagination.total = res.result.total;
        //   }
        // })
        getUserList(params).then((res) => {
          var that = this;
          if (res.success) {
            console.log(res,'刚开始进来');
            that.dataSource1 = res.result.records;
            // for (let i=0;i<that.dataSource1.length;i++) {
              // if (that.dataSource1[i].departNames.indexOf(",") != -1) {
              //   let arr = that.dataSource1[i].departNames.split(",");
              //   that.dataSource1[i].departNames = arr[0];
              //   console.log(that.dataSource1[i].departNames,'错错错吧是你的错');
              //   console.log(that.dataSource1[i],'错错错吧是你的错+1');
              // }
            // }
            // that.assignRoleName(that.dataSource);
            that.ipagination.total = res.result.total;
          }
        })
      },
      latestTwoSelected(arg) {
        // let params = {};
        if(arg===1){
          this.ipagination3.current = 1;
        }
        var params = this.getQueryParams(this.ipagination3);
        getLastlatestTwoList(params).then((res) => {
          console.log(res,'最近两天');
          if (res.success) {
            this.dataSource3 = res.result.records;
            // this.assignRoleName(this.dataSource);
            this.ipagination3.total = res.result.total;
          }
        })
      },
      getQueryParams(ipagination){
        var param = Object.assign({}, this.queryParam,this.isorter);
        param.field = this.getQueryField();
        param.pageNo = ipagination.current;
        param.pageSize = ipagination.pageSize;
        return filterObj(param);
      },
      getQueryField(){
        //TODO 字段权限控制
      },
      onSelectAll (selected, selectedRows, changeRows) {
        if (selected===true) {
          for (let a = 0;a<changeRows.length;a++) {
            var c = true;
            for (let b = 0;b<this.dataSource2.length;b++) {
              if (changeRows[a].username == this.dataSource2[b].username) {
                  c = false;
              }else{    
              }
            }
            if(c){
              this.dataSource2.push(changeRows[a]);
            }        
          }
        } else {
          for(var b = 0;b<changeRows.length;b++){
            this.dataSource2.splice(this.dataSource2.indexOf(changeRows[b]),1);
          }
        }

        // if(selected===true){
        //   for(var a = 0;a<changeRows.length;a++){
        //     this.dataSource2.push(changeRows[a]);
        //   }
        // }else{
        //   for(var b = 0;b<changeRows.length;b++){
        //     this.dataSource2.splice(this.dataSource2.indexOf(changeRows[b]),1);
        //   }
        // }
      },
      onSelect (record,selected) {
        if(selected===true){
          if (this.dataSource2 == null) {
            this.dataSource2 = [];
          }
          for (let i = 0;i<this.dataSource2.length;i++) {
            if (this.dataSource2[i].username == record.username) {
              return;
            }
          }
          this.dataSource2.push(record);
        }else{
          var index = this.dataSource2.indexOf(record);
          if(index >=0 ){
            this.dataSource2.splice(this.dataSource2.indexOf(record),1);
          }

        }
      },
      onSelectChange (selectedRowKeys,selectedRows) {
        this.selectedRowKeys = selectedRowKeys;
        this.selectionRows = selectedRows;
      },
      onClearSelected(){
        this.selectedRowKeys = [];
        this.selectionRows = [];
      },
      handleDelete: function(record){
        this.dataSource2.splice(this.dataSource2.indexOf(record),1);
      },
      handleTableChange(pagination, filters, sorter){
        //分页、排序、筛选变化时触发
        //TODO 筛选
        if (Object.keys(sorter).length>0){
          this.isorter.column = sorter.field;
          this.isorter.order = "ascend"==sorter.order?"asc":"desc"
        }
        this.ipagination = pagination;
        this.loadData();
      },
      handleTableChange3(pagination, filters, sorter){
        //分页、排序、筛选变化时触发
        //TODO 筛选
        if (Object.keys(sorter).length>0){
          this.isorter.column = sorter.field;
          this.isorter.order = "ascend"==sorter.order?"asc":"desc"
        }
        this.ipagination3 = pagination;
        this.latestTwoSelected();
      },
      queryDepartTree() {
        queryDepartTreeList().then((res) => {
          if (res.success) {
            this.departTree = res.result;
          }
        })
      },
      // 点击树节点,筛选出对应的用户
      onSelectDepart(selectedKeys) {
        if (selectedKeys[0] != null) {
          this.queryUserByDepId(selectedKeys); // 调用方法根据选选择的id查询用户信息
          if (this.selectedKeys[0] !== selectedKeys[0]) {
            this.selectedKeys = [selectedKeys[0]];
          }
        }
      },
      // 根据选择的id来查询用户信息
      queryUserByDepId(selectedKeys) {
        queryUserByDepId({ departId: selectedKeys.toString() }).then((res) => {
          if (res.success) {
            this.dataSource1 = res.result;
            // for (let i=0;this.dataSource1.length;i++) {
            //   if (this.dataSource1[i].departName != null) {
            //     this.dataSource1[i].departNames = this.dataSource1[i].departName;
            //   }             
            // }
            this.ipagination.total = res.result.length;
            // this.assignRoleName(this.dataSource);
          }
        })
      },
      byUserList() {
        // getAction(this.url.UserList,{usernames: this.selectListUser}).then((res)=>{
        //   console.log(res,'征战一下');
        //   this.dataSource2 = res.result;
        //   this.handleOk();
        // })
        // this.dataSource2.concat(this.selectListUser);
        this.dataSource2 = this.selectListUser;
        // this.dataSource2.push.apply(this.dataSource2,this.selectListUser);
        this.$emit("selectFinished",this.dataSource2);
      },
      clearSelected() {
        this.dataSource2 = [];
        this.$emit("selectFinished",this.dataSource2);
      }
    },
    watch: {
      selectListUser() {
        this.byUserList();
      },
      // cancelSelect() {
      //   if (this.cancelSelect) {
      //     this.dataSource2 = [];
      //     this.$emit("selectFinished",this.dataSource2);
      //   }
      // }
    },
    computed: {
      selectLng() {
        return this.dataSource2.length;
      }
    }
  }
</script>
<style lang="less" scoped>
  .ant-card-body .table-operator{
    margin-bottom: 18px;
  }
  .ant-table-tbody .ant-table-row td{
    padding-top:15px;
    padding-bottom:15px;
  }
  .anty-row-operator button{margin: 0 5px}
  .ant-btn-danger{background-color: #ffffff}

  .ant-modal-cust-warp{height: 100%}
  .ant-modal-cust-warp .ant-modal-body{height:calc(100% - 110px) !important;overflow-y: auto}
  .ant-modal-cust-warp .ant-modal-content{height:90% !important;overflow-y: hidden}

  .ant-modal {
    border: 1px solid red;
  }
  .title {
    width: 100%;
    padding: 0px 24px 24px 24px;
    div {
      font-size:16px;
      font-family:MicrosoftYaHeiUI;
      font-weight:bold;
      color:rgba(25,25,25,1);
      line-height:32px;
    }
    .closeIcon {
      cursor: pointer;
      border: 0;
      background: transparent;
      position: absolute;
      right: 0;
      top: 0;
      z-index: 10;
      font-weight: 700;
      line-height: 1;
      text-decoration: none;
      transition: color 0.3s;
      color: rgba(0, 0, 0, 0.45);
      outline: 0;
      padding: 0;
      display: block;
      font-style: normal;
      text-align: center;
      text-transform: none;
      text-rendering: auto;
      width: 76px;
      height: 86px;
      line-height: 86px;
      font-size: 16px;
    }
  }
  .btnClass {
    // padding: 32px 32px 0px 32px;
    margin-top: 20px;
    text-align: right;
    div {
      display: inline-block;
      margin-right: 20px;
      :nth-child(2) {
        font-weight: bold;
      }
    }
    button {
      margin: 0;
      padding: 0;
      border: 1px solid transparent;  //自定义边框
      outline: none;  //消除默认点击蓝色边框效果
    }
    .cancel {
      min-width:96px;
      height:40px;
      background:rgba(238,238,238,1);
      border-radius:4px;
      color:rgba(51,51,51,1);
      padding: 5px;
    }
    .confirm {
      min-width:96px;
      height:40px;
      // background:rgba(109,98,255,1);
      border-radius:4px;
      // color: rgba(255,255,255,1);
      padding: 5px;
    }
  }
  // 这是tabs样式
    .ant-tabs {
      :global(.ant-tabs-bar) {
        border-bottom: unset;
        margin-bottom: unset;
        :global(.ant-tabs-nav-container) {
          :global(.ant-tabs-nav-wrap) {
            :global(.ant-tabs-nav) {
              :global(.ant-tabs-tab){
                width:100px!important;
                text-align: center!important;
                padding: unset!important;
                background: #fff!important;
                margin-right: unset!important;
                border:1px solid #EFF1F2!important;
              }
              :global(.ant-tabs-tab:first-child){
                border-top-right-radius:unset!important;
              }
              :global(.ant-tabs-tab:nth-child(2)){
                border-top-left-radius:unset!important;
              }
              :global(.ant-tabs-tab-active) {
                background: #EFF1F2!important;
                // border-bottom: unset!important;
              }

            }
          }
        }
      }
      // 表格头部颜色样式 
      :global(.ant-tabs-content) {
        :global(.ant-table) {
          :global(.ant-table-thead > tr > th) {
            background: #EFF1F2!important;
          }
        }
      }
    }
    // 已选择人员table头部颜色
    .selectedTable {
      :global(.ant-table-content){
        :global(.ant-table-thead > tr > th) {
          background: #EFF1F2!important;
        }
      }
    }
</style>