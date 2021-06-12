<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="卡号" prop="cardNum">
      <el-input v-model="dataForm.cardNum" placeholder="卡号"></el-input>
    </el-form-item>
    <el-form-item label="密码" prop="password">
      <el-input v-model="dataForm.password" placeholder="密码"></el-input>
    </el-form-item>
    <el-form-item label="店铺id" prop="shopId">
      <el-input v-model="dataForm.shopId" placeholder="店铺id"></el-input>
    </el-form-item>
    <el-form-item label="卡金额" prop="price">
      <el-input v-model="dataForm.price" placeholder="卡金额"></el-input>
    </el-form-item>
    <el-form-item label="0 未使用 1 已使用" prop="status">
      <el-input v-model="dataForm.status" placeholder="0 未使用 1 已使用"></el-input>
    </el-form-item>
    <el-form-item label="创建时间" prop="createTime">
      <el-input v-model="dataForm.createTime" placeholder="创建时间"></el-input>
    </el-form-item>
    <el-form-item label="使用时间" prop="userTime">
      <el-input v-model="dataForm.userTime" placeholder="使用时间"></el-input>
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
          cardNum: '',
          password: '',
          shopId: '',
          price: '',
          status: '',
          createTime: '',
          userTime: ''
        },
        dataRule: {
          cardNum: [
            { required: true, message: '卡号不能为空', trigger: 'blur' }
          ],
          password: [
            { required: true, message: '密码不能为空', trigger: 'blur' }
          ],
          shopId: [
            { required: true, message: '店铺id不能为空', trigger: 'blur' }
          ],
          price: [
            { required: true, message: '卡金额不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '0 未使用 1 已使用不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '创建时间不能为空', trigger: 'blur' }
          ],
          userTime: [
            { required: true, message: '使用时间不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/x_shop_card/xshopcard/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.cardNum = data.xShopCard.cardNum
                this.dataForm.password = data.xShopCard.password
                this.dataForm.shopId = data.xShopCard.shopId
                this.dataForm.price = data.xShopCard.price
                this.dataForm.status = data.xShopCard.status
                this.dataForm.createTime = data.xShopCard.createTime
                this.dataForm.userTime = data.xShopCard.userTime
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
              url: this.$http.adornUrl(`/x_shop_card/xshopcard/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'cardNum': this.dataForm.cardNum,
                'password': this.dataForm.password,
                'shopId': this.dataForm.shopId,
                'price': this.dataForm.price,
                'status': this.dataForm.status,
                'createTime': this.dataForm.createTime,
                'userTime': this.dataForm.userTime
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
