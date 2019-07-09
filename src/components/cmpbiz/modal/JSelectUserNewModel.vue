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
        用户列表
      </div>
      <a-icon type="close" class="closeIcon" @click="handleCancel"/>
    </div>
    <a-row :gutter="18">
      <a-col :span="4">
        <a-directory-tree
          selectable
          :selectedKeys="selectedKeys"
          :checkStrictly="true"
          @select="this.onSelectDepart"
          :dropdownStyle="{maxHeight:'200px',overflow:'auto'}"
          :treeData="departTree"
          defaultExpandAll
        />
      </a-col>
      <a-col :span="12">
        <a-card title="选择人员" :bordered="true">
          <!-- 查询区域 -->
          <div class="table-page-search-wrapper">
            <a-form layout="inline">
              <a-row :gutter="24">

                <a-col :span="10">
                  <a-form-item label="姓名">
                    <a-input placeholder="请输入姓名" v-model="queryParam.realname"></a-input>
                  </a-form-item>
                </a-col>
                <a-col :span="8" >
                  <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
                    <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
                    <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
                  </span>
                </a-col>

              </a-row>
            </a-form>
          </div>
          <!-- table区域-begin -->
          <div>
            <a-table
              size="small"
              bordered
              rowKey="id"
              :columns="columns1"
              :dataSource="dataSource1"
              :pagination="ipagination"
              :loading="loading"
              :scroll="{ y: 240 }"
              :rowSelection="{selectedRowKeys: selectedRowKeys,onSelectAll:onSelectAll,onSelect:onSelect,onChange: onSelectChange}"
              @change="handleTableChange">
            </a-table>
          </div>
          <!-- table区域-end -->
        </a-card>
      </a-col>
      <a-col :span="8">
        <a-card title="用户选择" :bordered="true">
          <!-- table区域-begin -->
          <div>
            <a-table
              size="small"
              bordered
              rowKey="id"
              :columns="columns2"
              :dataSource="dataSource2"
              :loading="loading"
              :scroll="{ y: 240 }"
            >
              <span slot="action" slot-scope="text, record">
                <a @click="handleDelete(record)">删除</a>
                <!-- <a-button type="primary" size="small" @click="handleDelete(record)" icon="delete">删除</a-button> -->
              </span>
            </a-table>
          </div>
          <!-- table区域-end -->
        </a-card>
      </a-col>
    </a-row>
    <a-row :gutter="18">
        <div class="btnClass">
          <a-button @click="handleOk" icon="check" type="primary" style="margin-right:10px" class="confirm">确定</a-button>
          <a-button @click="handleCancel" icon="close" style="margin-right:10px" class="cancel">关闭</a-button>
        </div>
    </a-row>
  </a-modal>
</template>

<script>
  import { filterObj } from '@/utils/util'
  import { getAction } from '@/api/manage'
  import { queryDepartTreeList, getUserList, queryUserByDepId } from '@/api/api'

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
            title: '用户名',
            align:"center",
            width:113,
            dataIndex: 'username'
          },
          {
            title: '姓名',
            align:"center",
            width:100,
            dataIndex: 'realname'
          }
        ],
        columns2: [
          {
            title: '用户名',
            align:"center",
            width:80,
            dataIndex: 'username'
          },
          {
            title: '姓名',
            align:"center",
            width:80,
            dataIndex: 'realname'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            width:60,
            scopedSlots: { customRender: 'action' },
          }
        ],
        //数据集
        dataSource1:[],
        dataSource2:[],
        // 分页参数
        ipagination:{
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
        var params = this.getQueryParams();//查询条件
        // getAction(this.url.list,params).then((res)=>{
        //   if(res.success){
        //     this.dataSource1 = res.result.records;
        //     this.ipagination.total = res.result.total;
        //   }
        // })
        getUserList(params).then((res) => {
          if (res.success) {
            this.dataSource1 = res.result.records;
            // this.assignRoleName(this.dataSource);
            this.ipagination.total = res.result.total;
          }
        })
      },
      getQueryParams(){
        var param = Object.assign({}, this.queryParam,this.isorter);
        param.field = this.getQueryField();
        param.pageNo = this.ipagination.current;
        param.pageSize = this.ipagination.pageSize;
        return filterObj(param);
      },
      getQueryField(){
        //TODO 字段权限控制
      },
      onSelectAll (selected, selectedRows, changeRows) {
        if(selected===true){
          for(var a = 0;a<changeRows.length;a++){
            this.dataSource2.push(changeRows[a]);
          }
        }else{
          for(var b = 0;b<changeRows.length;b++){
            this.dataSource2.splice(this.dataSource2.indexOf(changeRows[b]),1);
          }
        }
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
    padding: 32px 32px 0px 32px;
    text-align: center;
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
</style>