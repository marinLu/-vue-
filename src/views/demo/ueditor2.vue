<template>
  <div>
    <VueUeditorWrap ref="child" @getMsg="gettt"></VueUeditorWrap>
    <el-button type="primary" @click="sss2()">getMsg</el-button>
  </div>
</template>
<script>
  import VueUeditorWrap from '../../components/ueditor/ueditor.vue'
  import ElButton from "../../../node_modules/element-ui/packages/button/src/button";
  export default {
    components: {
      ElButton,
      VueUeditorWrap
    },
    data() {
      return {
      }
    },
    methods:{
      sss2:function () {
        this.$refs.child.getMsg()
      },
      gettt(msg){
          console.log(msg)
      }
    }
  }
</script>
