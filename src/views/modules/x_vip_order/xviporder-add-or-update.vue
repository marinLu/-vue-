<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="用户id" prop="userId">
      <el-input v-model="dataForm.userId" placeholder="用户id"></el-input>
    </el-form-item>
    <el-form-item label="购买时间" prop="createDate">
      <el-input v-model="dataForm.createDate" placeholder="购买时间"></el-input>
    </el-form-item>
    <el-form-item label="有效期,单位月" prop="validTime">
      <el-input v-model="dataForm.validTime" placeholder="有效期,单位月"></el-input>
    </el-form-item>
    <el-form-item label="截止日期" prop="expirationDate">
      <el-input v-model="dataForm.expirationDate" placeholder="截止日期"></el-input>
    </el-form-item>
    <el-form-item label="付款金额" prop="payPrice">
      <el-input v-model="dataForm.payPrice" placeholder="付款金额"></el-input>
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
          userId: '',
          createDate: '',
          validTime: '',
          expirationDate: '',
          payPrice: ''
        },
        dataRule: {
          userId: [
            { required: true, message: '用户id不能为空', trigger: 'blur' }
          ],
          createDate: [
            { required: true, message: '购买时间不能为空', trigger: 'blur' }
          ],
          validTime: [
            { required: true, message: '有效期,单位月不能为空', trigger: 'blur' }
          ],
          expirationDate: [
            { required: true, message: '截止日期不能为空', trigger: 'blur' }
          ],
          payPrice: [
            { required: true, message: '付款金额不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/x_vip_order/xviporder/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.userId = data.xVipOrder.userId
                this.dataForm.createDate = data.xVipOrder.createDate
                this.dataForm.validTime = data.xVipOrder.validTime
                this.dataForm.expirationDate = data.xVipOrder.expirationDate
                this.dataForm.payPrice = data.xVipOrder.payPrice
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
              url: this.$http.adornUrl(`/x_vip_order/xviporder/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'userId': this.dataForm.userId,
                'createDate': this.dataForm.createDate,
                'validTime': this.dataForm.validTime,
                'expirationDate': this.dataForm.expirationDate,
                'payPrice': this.dataForm.payPrice
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
