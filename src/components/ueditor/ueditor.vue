<template>
  <div>
    <component
      style="width:100%!important"
      :is="currentViewComp"
      transition="fade"
      transition-mode="out-in"
      :config="ueditorConfig"
      :destroy="true"
      @ready="ueReady" v-model="msg">
    </component>
  </div>
</template>
<script>
  import VueUeditorWrap from 'vue-ueditor-wrap'
//  import '../../../static/plugins/ueditor-1.4.3.3/ueditor.config.js'
//  import '../../../static/plugins/ueditor-1.4.3.3/ueditor.all.js'
//  import '../../../static/plugins/ueditor-1.4.3.3/lang/zh-cn/zh-cn.js'

  export default {
    components: {
      VueUeditorWrap
    },
    props:{
      message:String
    },
    data() {
      return {
        msg:'',
        currentViewComp: null,
        ueditorConfig: {
          serverUrl: 'https://zds.sz-baihui.com/chebupa/api/UE/ueUpload',
//          UEDITOR_HOME_URL: '/static/plugins/ueditor-1.4.3.3/',
          UEDITOR_HOME_URL: window.SITE_CONFIG.version+'/static/plugins/ueditor-1.4.3.3/',
          initialContent: '',
          initialFrameHeight: 250,
          initialFrameWidth: 800,
          autoHeightEnabled: false
        }
      }
    },
    created(){
      this.msg = this.message
    },
    watch:{
      message:function (value) {
        this.msg = value
      }
    },
    mounted() {
      this.currentViewComp = VueUeditorWrap
    },
    destroyed() {
      this.currentViewComp = null
    },
    methods: {
      ueReady(editorInstance) {
        this.$nextTick(() => {
//          editorInstance.setContent('')
        })
      },
      getMsg(){
        this.$emit('getMsg',this.msg)
      }
    }
  }
</script>
