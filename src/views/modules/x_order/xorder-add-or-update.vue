<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="订单号" prop="orderNum">
      <el-input v-model="dataForm.orderNum" placeholder="订单号"></el-input>
    </el-form-item>
    <el-form-item label="商品列表id" prop="commodityIds">
      <el-input v-model="dataForm.commodityIds" placeholder="商品列表id"></el-input>
    </el-form-item>
    <el-form-item label="商品购买数量" prop="commodityNums">
      <el-input v-model="dataForm.commodityNums" placeholder="商品购买数量"></el-input>
    </el-form-item>
    <el-form-item label="订单金额" prop="orderPrice">
      <el-input v-model="dataForm.orderPrice" placeholder="订单金额"></el-input>
    </el-form-item>
    <el-form-item label="支付金额" prop="payPrice">
      <el-input v-model="dataForm.payPrice" placeholder="支付金额"></el-input>
    </el-form-item>
    <el-form-item label="创建时间" prop="createTime">
      <el-input v-model="dataForm.createTime" placeholder="创建时间"></el-input>
    </el-form-item>
    <el-form-item label="状态,0未支付,1已支付" prop="status">
      <el-input v-model="dataForm.status" placeholder="状态,0未支付,1已支付"></el-input>
    </el-form-item>
    <el-form-item label="支付时间" prop="payTime">
      <el-input v-model="dataForm.payTime" placeholder="支付时间"></el-input>
    </el-form-item>
    <el-form-item label="优惠券金额" prop="coupon">
      <el-input v-model="dataForm.coupon" placeholder="优惠券金额"></el-input>
    </el-form-item>
    <el-form-item label=" 使用钱包金额" prop="wallet">
      <el-input v-model="dataForm.wallet" placeholder=" 使用钱包金额"></el-input>
    </el-form-item>
    <el-form-item label="用户优惠券id" prop="userCouponId">
      <el-input v-model="dataForm.userCouponId" placeholder="用户优惠券id"></el-input>
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
          commodityIds: '',
          commodityNums: '',
          orderPrice: '',
          payPrice: '',
          createTime: '',
          status: '',
          payTime: '',
          coupon: '',
          wallet: '',
          userCouponId: ''
        },
        dataRule: {
          orderNum: [
            { required: true, message: '订单号不能为空', trigger: 'blur' }
          ],
          commodityIds: [
            { required: true, message: '商品列表id不能为空', trigger: 'blur' }
          ],
          commodityNums: [
            { required: true, message: '商品购买数量不能为空', trigger: 'blur' }
          ],
          orderPrice: [
            { required: true, message: '订单金额不能为空', trigger: 'blur' }
          ],
          payPrice: [
            { required: true, message: '支付金额不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '创建时间不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '状态,0未支付,1已支付不能为空', trigger: 'blur' }
          ],
          payTime: [
            { required: true, message: '支付时间不能为空', trigger: 'blur' }
          ],
          coupon: [
            { required: true, message: '优惠券金额不能为空', trigger: 'blur' }
          ],
          wallet: [
            { required: true, message: ' 使用钱包金额不能为空', trigger: 'blur' }
          ],
          userCouponId: [
            { required: true, message: '用户优惠券id不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/x_order/xorder/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.orderNum = data.xOrder.orderNum
                this.dataForm.commodityIds = data.xOrder.commodityIds
                this.dataForm.commodityNums = data.xOrder.commodityNums
                this.dataForm.orderPrice = data.xOrder.orderPrice
                this.dataForm.payPrice = data.xOrder.payPrice
                this.dataForm.createTime = data.xOrder.createTime
                this.dataForm.status = data.xOrder.status
                this.dataForm.payTime = data.xOrder.payTime
                this.dataForm.coupon = data.xOrder.coupon
                this.dataForm.wallet = data.xOrder.wallet
                this.dataForm.userCouponId = data.xOrder.userCouponId
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
              url: this.$http.adornUrl(`/x_order/xorder/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'orderNum': this.dataForm.orderNum,
                'commodityIds': this.dataForm.commodityIds,
                'commodityNums': this.dataForm.commodityNums,
                'orderPrice': this.dataForm.orderPrice,
                'payPrice': this.dataForm.payPrice,
                'createTime': this.dataForm.createTime,
                'status': this.dataForm.status,
                'payTime': this.dataForm.payTime,
                'coupon': this.dataForm.coupon,
                'wallet': this.dataForm.wallet,
                'userCouponId': this.dataForm.userCouponId
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
