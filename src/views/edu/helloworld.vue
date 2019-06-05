<template>
<div>{{testData}}</div>

</template>

<script>
  import {getAction} from '@api/manage'

  export default {
     name:'helloworld',
     data(){
       return {
         testData:"",
         url:"/test/cmpDemo/testData"
       }
     },
     methods:{
     loadData(){
         getAction(this.url).then((res)=>{
          this.testData=res;
         })
      }
    },
    created(){
    this.loadData()
    }
  }
</script>

<style scoped>

</style>