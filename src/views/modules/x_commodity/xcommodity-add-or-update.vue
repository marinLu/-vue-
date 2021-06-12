<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="120px">
    <el-form-item label="商品名称" prop="commodityName">
      <el-input v-model="dataForm.commodityName" placeholder="商品名称"></el-input>
    </el-form-item>
      <el-form-item label="轮播图" prop="commodityBanner">
        <el-upload
          class="avatar-uploader"
          :action="url"
          :before-upload="beforeUploadHandle"
          :on-success="successHandle"
          multiple
          :show-file-list="false"
        >
          <img v-if="dataForm.commodityBanner" :src="dataForm.commodityBanner" class="avatar"  style="max-height: 200px;max-width: 200px">
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>
      </el-form-item>
      <el-form-item label="详情图" prop="commodityDetail">
        <el-upload
          class="avatar-uploader"
          :action="url"
          :before-upload="beforeUploadHandle2"
          :on-success="successHandle2"
          multiple
          :show-file-list="false"
        >
          <img v-if="dataForm.commodityDetail" :src="dataForm.commodityDetail" class="avatar"  style="max-height: 200px;max-width: 200px">
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>
      </el-form-item>
    <el-form-item label="商品价格" prop="commodityPrice">
      <el-input v-model="dataForm.commodityPrice" placeholder="商品价格"></el-input>
    </el-form-item>
      <el-form-item label="商品折扣价格" prop="commodityDiscountPrice">
        <el-input v-model="dataForm.commodityDiscountPrice" placeholder="商品折扣价格"></el-input>
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
        visible: false,
        url:'',
        dataForm: {
          id: 0,
          commodityName: '',
          commodityBanner: '',
          commodityPrice: '',
          commodityDiscountPrice:'',
          commodityDetail:''
        },
        dataRule: {
          commodityName: [
            { required: true, message: '商品名称不能为空', trigger: 'blur' }
          ],
          commodityBanner: [
            { required: true, message: '列表小图不能为空', trigger: 'blur' }
          ],
          commodityPrice: [
            { required: true, message: '商品价格不能为空', trigger: 'blur' }
          ],
          commodityDetail: [
            { required: true, message: '详情图不能为空', trigger: 'blur' }
          ],
          commodityDiscountPrice: [
            { required: true, message: '商品折扣价格不能为空', trigger: 'blur' }
          ]

        }
      }
    },
    methods: {
      init (id) {
        this.url = this.$http.adornUrl(`/sys/oss/upload?token=${JSON.parse(sessionStorage.getItem('token'))}`)
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/x_commodity/xcommodity/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.commodityName = data.xCommodity.commodityName
                this.dataForm.commodityBanner = data.xCommodity.commodityBanner
                this.dataForm.commodityPrice = data.xCommodity.commodityPrice
                 this.dataForm.commodityDetail = data.xCommodity.commodityDetail
                this.dataForm.commodityDiscountPrice = data.xCommodity.commodityDiscountPrice
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
              url: this.$http.adornUrl(`/x_commodity/xcommodity/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'commodityName': this.dataForm.commodityName,
                'commodityBanner': this.dataForm.commodityBanner,
                'commodityPrice': this.dataForm.commodityPrice,
                'commodityDetail':this.dataForm.commodityDetail,
                'commodityDiscountPrice':this.dataForm.commodityDiscountPrice
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
        console.log("上传返回结果"+response.url)
        if (response && response.code === 0) {
          this.dataForm.commodityBanner = response.url;
        } else {
          this.$message.error(response.msg)
        }
      },
      // 上传之前
      beforeUploadHandle2 (file) {
        if (file.type !== 'image/jpg' && file.type !== 'image/jpeg' && file.type !== 'image/png' && file.type !== 'image/gif') {
          this.$message.error('只支持jpg、png、gif格式的图片！')
          return false
        }
        this.num++
      },
      // 上传成功
      successHandle2 (response, file) {
        console.log("上传返回结果"+response.url)
        if (response && response.code === 0) {
          this.dataForm.commodityDetail = response.url;
        } else {
          this.$message.error(response.msg)
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
