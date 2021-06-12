<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="用户id" prop="userId">
      <el-input v-model="dataForm.userId" placeholder="用户id"></el-input>
    </el-form-item>
    <el-form-item label="店铺id" prop="shopId">
      <el-input v-model="dataForm.shopId" placeholder="店铺id"></el-input>
    </el-form-item>
    <el-form-item label="提现金额" prop="extractMoney">
      <el-input v-model="dataForm.extractMoney" placeholder="提现金额"></el-input>
    </el-form-item>
    <el-form-item label="提现时间" prop="createTime">
      <el-input v-model="dataForm.createTime" placeholder="提现时间"></el-input>
    </el-form-item>
    <el-form-item label="0等待审核,-1被禁止,1审核通过已打款" prop="status">
      <el-input v-model="dataForm.status" placeholder="0等待审核,-1被禁止,1审核通过已打款"></el-input>
    </el-form-item>
    <el-form-item label="审核时间" prop="reviewTime">
      <el-input v-model="dataForm.reviewTime" placeholder="审核时间"></el-input>
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
          shopId: '',
          extractMoney: '',
          createTime: '',
          status: '',
          reviewTime: ''
        },
        dataRule: {
          userId: [
            { required: true, message: '用户id不能为空', trigger: 'blur' }
          ],
          shopId: [
            { required: true, message: '店铺id不能为空', trigger: 'blur' }
          ],
          extractMoney: [
            { required: true, message: '提现金额不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '提现时间不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '0等待审核,-1被禁止,1审核通过已打款不能为空', trigger: 'blur' }
          ],
          reviewTime: [
            { required: true, message: '审核时间不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/x_user_extract/xuserextract/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.userId = data.xUserExtract.userId
                this.dataForm.shopId = data.xUserExtract.shopId
                this.dataForm.extractMoney = data.xUserExtract.extractMoney
                this.dataForm.createTime = data.xUserExtract.createTime
                this.dataForm.status = data.xUserExtract.status
                this.dataForm.reviewTime = data.xUserExtract.reviewTime
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
              url: this.$http.adornUrl(`/x_user_extract/xuserextract/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'userId': this.dataForm.userId,
                'shopId': this.dataForm.shopId,
                'extractMoney': this.dataForm.extractMoney,
                'createTime': this.dataForm.createTime,
                'status': this.dataForm.status,
                'reviewTime': this.dataForm.reviewTime
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
