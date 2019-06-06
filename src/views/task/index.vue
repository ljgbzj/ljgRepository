<template>
  <a-tabs defaultActiveKey="1" >
    <a-tab-pane tab="任务待办" key="1">
      <task-list></task-list>
    </a-tab-pane>
    <a-tab-pane tab="流程跟踪" key="2">
      <task-track></task-track>
    </a-tab-pane>
    <a-tab-pane tab="表单查询" key="3">
      <task-search></task-search>
    </a-tab-pane>
  </a-tabs>
</template>

<script>

  import taskList from './TaskList'
  import taskSearch from './search'
  import taskTrack from './track'

  export default {
    name: "task",
    components: {
      taskList,
      taskSearch,
      taskTrack
    }
  }

</script>

