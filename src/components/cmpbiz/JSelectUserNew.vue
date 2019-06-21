<template>
  <div style="width: 100%;">
    <a-select
      mode="multiple"
      placeholder="请选择人员"
      :value="nameList"
      style="width: calc(100% - 178px);"
      :open="false"
      @deselect="selectDe"
      >
    </a-select>
    <span style="display: inline-block;width:170px;float: right;overflow: hidden;">
      <a-button type="primary" @click="handleSelect" icon="search" style="width: 81px">选择</a-button>
      <a-button type="primary" @click="selectReset" icon="reload" style="margin-left: 8px;width: 81px">清空</a-button>
    </span>

    <!-- 选择多个用户支持排序 -->
    <j-select-user-new-model ref="selectModal" @selectFinished="selectOK"></j-select-user-new-model>
  </div>
</template>

<script>
  import JSelectUserNewModel from './modal/JSelectUserNewModel'
  export default {
    name: 'JSelectUserNew',
    components:{ JSelectUserNewModel },
    props:{
      value:{
        type:String,
        required:false
      },
      triggerChange:{
        type: Boolean,
        required: false,
        default: false
      },
      cancelSelect: {
        type: Boolean,
        required: false,
        default: false
      }
    },
    data(){
      return {
        selectList: [],
      }
    },
    computed: {
      nameList: function () {
        var names = [];
        var usernames = [];
        for (var a = 0; a < this.selectList.length; a++) {
          names.push(this.selectList[a].realname);
          usernames.push(this.selectList[a].username);
        }
        let nameStr = '';
        let userNameStr = '';
        if(names.length>0){
          nameStr = names.join(",")
        }
        if(usernames.length>0){
          userNameStr = usernames.join(",")
        }
        if(this.triggerChange){
          this.$emit("change",nameStr)
        }else{
          this.$emit("input",nameStr)
        }
        if(this.triggerChange){
          this.$emit("userName",userNameStr)
        }else{
          this.$emit("userName",userNameStr)
        }
        if (this.cancelSelect == true) {
          this.selectList = [];
        }
        return names;
      }
    },
    methods:{
      handleSelect: function () {
        this.$refs.selectModal.add();
      },
      selectReset() {
        this.selectList = [];
      },
      selectDe(value) {
        for (let i = 0; i<this.selectList.length; i++) {
          if (this.selectList[i].realname == value) {
            this.selectList.splice(i,1);
          }
        }
      },
      selectOK: function (data) {
        this.selectList = data;
      }
    },
    created() {
    }
  }
</script>

<style lang="less" scoped>
  .components-input-demo-presuffix .anticon-close-circle {
    cursor: pointer;
    color: #ccc;
    transition: color 0.3s;
    font-size: 12px;
  }
  .components-input-demo-presuffix .anticon-close-circle:hover {
    color: #f5222d;
  }
  .components-input-demo-presuffix .anticon-close-circle:active {
    color: #666;
  }

  .btns {
    i:hover {
      cursor: pointer;
    }
  }
</style>
