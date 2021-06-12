<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="100px">
      <el-form-item label="轮播图所属"  prop="code">
        <el-select v-model="dataForm.code" placeholder="请选择" @change="changeCode">
          <el-option label="首页轮播图" value="home"></el-option>
          <el-option label="商城轮播图" value="commodity"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="轮播图" prop="banner">
        <el-upload
          class="avatar-uploader"
          :action="url"
          :before-upload="beforeUploadHandle"
          :on-success="successHandle"
          multiple
          :show-file-list="false"
        >
          <img v-if="dataForm.banner" :src="dataForm.banner" class="avatar"  style="max-height: 200px;max-width: 200px">
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>
      </el-form-item>
      <el-form-item label="绑定商品id" v-if="bindVisble==true">
        <el-select v-model="dataForm.bindId" autocomplete="off" filterable style="width:300px">
          <el-option v-for="(item,index) in commodityList" :key="index" :label="item.commodityName" :value="item.id">
          </el-option>
        </el-select>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        bindVisble:false,
        visible: false,
        url:'',
        dataForm: {
          id: 0,
          code: '',
          banner: '',
          bindId:''
        },
        dataRule: {
          code: [
            { required: true, message: '轮播图所属不能为空', trigger: 'blur' }
          ],
          banner: [
            { required: true, message: '轮播图链接不能为空', trigger: 'blur' }
          ]
        },
        commodityList:[]
      }
    },
    methods: {
      init (id) {
        this.$http({
          url: this.$http.adornUrl('/x_commodity/xcommodity/getCommodityList'),
          method: 'get',
          params: this.$http.adornParams({})
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.commodityList = data.list
          } else {
            this.commodityList = []
          }
        })
        this.url = this.$http.adornUrl(`/sys/oss/upload?token=${JSON.parse(sessionStorage.getItem('token'))}`)
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/x_banner/xbanner/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.code = data.xBanner.code
                this.dataForm.banner = data.xBanner.banner
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/x_banner/xbanner/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'code': this.dataForm.code,
                'banner': this.dataForm.banner
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      },
      // 上传之前
      beforeUploadHandle (file) {
        if (file.type !== 'image/jpg' && file.type !== 'image/jpeg' && file.type !== 'image/png' && file.type !== 'image/gif') {
          this.$message.error('只支持jpg、png、gif格式的图片！')
          return false
        }
        this.num++
      },
      // 上传成功
      successHandle (response, file) {
        console.log('上传返回结果'+response.url)
        if (response && response.code === 0) {
          this.dataForm.banner = response.url;
        } else {
          this.$message.error(response.msg)
        }
      },
      changeCode:function () {
        console.log('------->',this.dataForm.code)
        if (this.dataForm.code=='commodity'){
          this.bindVisble = true;
        }
      }
    }
  }
</script>
<style>
  .avatar-uploader .el-upload {
    border: 1px dashed #d9d9d9;
    border-radius: 6px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
  }
  .avatar-uploader .el-upload:hover {
    border-color: #409EFF;
  }
  .avatar-uploader-icon {
    font-size: 28px;
    color: #8c939d;
    width: 178px;
    height: 178px;
    line-height: 178px;
    text-align: center;
  }
  .avatar {
    width: 178px;
    height: 178px;
    display: block;
  }
</style>
