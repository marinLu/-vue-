<template>
  <div>
    <component
      style="width:100%!important"
      :is="currentViewComp"
      transition="fade"
      transition-mode="out-in"
      :config="ueditorConfig"
      :destroy="true"
      @ready="ueReady">
    </component>
  </div>
</template>
<script>
  import VueUeditorWrap from 'vue-ueditor-wrap'
//  import { STATIC_PATH, UEDITOR_SERVER } from '@/config'
//  import '../../../static/plugins/ueditor-1.4.3.3/ueditor.config.js'
//  import '../../../static/plugins/ueditor-1.4.3.3/ueditor.all.js'
//  import '../../../static/plugins/ueditor-1.4.3.3/lang/zh-cn/zh-cn.js'
  export default {
    data() {
      return {
        currentViewComp: null,
        ueditorConfig: {
          serverUrl: 'https://zds.sz-baihui.com/chebupa/api/UE/ueUpload',
//          UEDITOR_HOME_URL: '/static/plugins/ueditor-1.4.3.3/',
          UEDITOR_HOME_URL: window.SITE_CONFIG.version+'/static/plugins/ueditor-1.4.3.3/',
          initialContent: '',
          initialFrameHeight: 400,
          initialFrameWidth: '100%',
          autoHeightEnabled: false
        }
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
          editorInstance.setContent('')
        })
      }
    }
  }
</script>
