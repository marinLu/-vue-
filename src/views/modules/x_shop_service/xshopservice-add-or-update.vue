<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="商店id" prop="shopId" v-if="false">
      <el-input v-model="dataForm.shopId" placeholder="商店id"></el-input>
    </el-form-item>
    <el-form-item label="服务名称" prop="serviceName">
      <el-input v-model="dataForm.serviceName" placeholder="服务名称"></el-input>
    </el-form-item>
    <el-form-item label="列表图" prop="serviceBanner">
      <el-upload
        class="avatar-uploader"
        :action="url"
        :before-upload="beforeUploadHandle"
        :on-success="successHandle"
        multiple
        :show-file-list="false"
      >
        <img v-if="dataForm.serviceBanner" :src="dataForm.serviceBanner" class="avatar"  style="max-height: 200px;max-width: 200px">
        <i v-else class="el-icon-plus avatar-uploader-icon"></i>
      </el-upload>
    </el-form-item>
    <el-form-item label="详情图" prop="serviceDetail">
      <el-upload
        class="avatar-uploader"
        :action="url"
        :before-upload="beforeUploadHandle2"
        :on-success="successHandle2"
        multiple
        :show-file-list="false"
      >
        <img v-if="dataForm.serviceDetail" :src="dataForm.serviceDetail" class="avatar"  style="max-height: 200px;max-width: 200px">
        <i v-else class="el-icon-plus avatar-uploader-icon"></i>
      </el-upload>
    </el-form-item>
    <el-form-item label="原价" prop="originalPrice">
      <el-input v-model="dataForm.originalPrice" placeholder="原价"></el-input>
    </el-form-item>
    <el-form-item label="是否折扣" prop="isDiscount">
      <el-select v-model="dataForm.isDiscount" placeholder="请选择">
        <el-option label="不折扣" value="0"></el-option>
        <el-option label="折扣" value="1"></el-option>
      </el-select>
    </el-form-item>
    <el-form-item label="折扣金额" prop="discountPrice">
      <el-input v-model="dataForm.discountPrice" placeholder="折扣金额"></el-input>
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
          shopId: '',
          serviceName: '',
          serviceBanner: '',
          serviceDetail: '',
          originalPrice: '',
          isDiscount: '0',
          discountPrice: ''
        },
        dataRule: {
          shopId: [
            { required: true, message: '商店id不能为空', trigger: 'blur' }
          ],
          serviceName: [
            { required: true, message: '服务名称不能为空', trigger: 'blur' }
          ],
          serviceBanner: [
            { required: true, message: '列表图不能为空', trigger: 'blur' }
          ],
          serviceDetail: [
            { required: true, message: '详情图不能为空', trigger: 'blur' }
          ],
          originalPrice: [
            { required: true, message: '原价不能为空', trigger: 'blur' }
          ],
          isDiscount: [
            { required: true, message: '是否折扣,0不折扣,1折扣不能为空', trigger: 'blur' }
          ],
          discountPrice: [
            { required: true, message: '折扣金额不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id,shopId) {
        this.url = this.$http.adornUrl(`/sys/oss/upload?token=${JSON.parse(sessionStorage.getItem('token'))}`)
        this.dataForm.id = id || 0
        this.dataForm.shopId = shopId || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/x_shop_service/xshopservice/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.shopId = data.xShopService.shopId
                this.dataForm.serviceName = data.xShopService.serviceName
                this.dataForm.serviceBanner = data.xShopService.serviceBanner
                this.dataForm.serviceDetail = data.xShopService.serviceDetail
                this.dataForm.originalPrice = data.xShopService.originalPrice
                this.dataForm.isDiscount = data.xShopService.isDiscount
                this.dataForm.discountPrice = data.xShopService.discountPrice
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
              url: this.$http.adornUrl(`/x_shop_service/xshopservice/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'shopId': this.dataForm.shopId,
                'serviceName': this.dataForm.serviceName,
                'serviceBanner': this.dataForm.serviceBanner,
                'serviceDetail': this.dataForm.serviceDetail,
                'originalPrice': this.dataForm.originalPrice,
                'isDiscount': this.dataForm.isDiscount,
                'discountPrice': this.dataForm.discountPrice,
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
          this.dataForm.serviceBanner = response.url;
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
          this.dataForm.serviceDetail = response.url;
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
