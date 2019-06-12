<template>
  <div class="desc">
    <div>工程建设管理云平台</div>
    <p>云办公平台专注服务于各大数字化系统，汇集存储各大系统的信息数据。平台有大量的组件可以直接使用，提高项目的开发效率，减少开发工作量。</p>
    <router-link to="/user/login">
      <button>进入平台</button>
    </router-link>
  </div>
</template>

<script>
export default {
  name: 'description'
}
</script>
<style lang="less" scoped>
.desc {
  margin: 218px 0 0 119px;
  div {
    height: 62px;
    font-size: 64px;
    font-family: SourceHanSansCN-Bold;
    font-weight: bold;
    color: rgba(255, 255, 255, 1);
    line-height: 40px;
    margin-bottom: 39px;
  }
  p {
    width: 360px;
    height: 80px;
    font-size: 16px;
    font-family: SourceHanSansCN-Normal;
    font-weight: 400;
    color: rgba(255, 255, 255, 1);
    line-height: 32px;
    margin-bottom: 59px;
  }
  a {
    button {
      margin-left: 1px;
      width: 140px;
      height: 44px;
      background: rgba(255, 255, 255, 1);
      border-radius: 4px;
      font-size: 16px;
      font-family: MicrosoftYaHei;
      font-weight: 400;
      color: rgba(107, 97, 254, 1);
      line-height: 32px;
    }
    button:hover {
      background: none;
      color: rgba(255, 255, 255, 1);
      border: 1px solid rgba(255, 255, 255, 1);
      cursor: pointer;
    }
  }
}
</style>


