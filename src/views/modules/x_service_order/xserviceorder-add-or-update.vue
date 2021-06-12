<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="服务订单号" prop="orderNum">
      <el-input v-model="dataForm.orderNum" placeholder="服务订单号"></el-input>
    </el-form-item>
    <el-form-item label="商店id" prop="shopId">
      <el-input v-model="dataForm.shopId" placeholder="商店id"></el-input>
    </el-form-item>
    <el-form-item label="支付金额" prop="payPrice">
      <el-input v-model="dataForm.payPrice" placeholder="支付金额"></el-input>
    </el-form-item>
    <el-form-item label="上传拍照订单链接" prop="orderPic">
      <el-input v-model="dataForm.orderPic" placeholder="上传拍照订单链接"></el-input>
    </el-form-item>
    <el-form-item label="创建时间" prop="createTime">
      <el-input v-model="dataForm.createTime" placeholder="创建时间"></el-input>
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
        dataForm: {
          id: 0,
          orderNum: '',
          shopId: '',
          payPrice: '',
          orderPic: '',
          createTime: ''
        },
        dataRule: {
          orderNum: [
            { required: true, message: '服务订单号不能为空', trigger: 'blur' }
          ],
          shopId: [
            { required: true, message: '商店id不能为空', trigger: 'blur' }
          ],
          payPrice: [
            { required: true, message: '支付金额不能为空', trigger: 'blur' }
          ],
          orderPic: [
            { required: true, message: '上传拍照订单链接不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '创建时间不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/x_service_order/xserviceorder/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.orderNum = data.xServiceOrder.orderNum
                this.dataForm.shopId = data.xServiceOrder.shopId
                this.dataForm.payPrice = data.xServiceOrder.payPrice
                this.dataForm.orderPic = data.xServiceOrder.orderPic
                this.dataForm.createTime = data.xServiceOrder.createTime
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
              url: this.$http.adornUrl(`/x_service_order/xserviceorder/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'orderNum': this.dataForm.orderNum,
                'shopId': this.dataForm.shopId,
                'payPrice': this.dataForm.payPrice,
                'orderPic': this.dataForm.orderPic,
                'createTime': this.dataForm.createTime
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
      }
    }
  }
</script>
