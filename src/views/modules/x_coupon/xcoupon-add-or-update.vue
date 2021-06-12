<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="150px">
    <el-form-item label="优惠券金额" prop="couponMoney">
      <el-input v-model="dataForm.couponMoney" placeholder="优惠券金额"></el-input>
    </el-form-item>
<!--    <el-form-item label="满足使用条件金额" prop="couponTermMoney">
      <el-input  @keyup.native='keyupEvent($event,input)' v-model="dataForm.couponTermMoney" placeholder="优惠券满足使用条件金额(元)"></el-input>
    </el-form-item>-->
      <el-form-item label="优惠券满足使用条件" prop="title">
        <el-input  v-model="dataForm.title" placeholder="优惠券满足使用条件"></el-input>
      </el-form-item>
    <el-form-item label="优惠券发放时间" prop="createTime">
      <el-date-picker
        v-model="dataForm.createTime"
        type="datetime"
        placeholder="选择日期">
      </el-date-picker>
    </el-form-item>
    <el-form-item label="有效期,单位天" prop="validNum">
      <el-input type="number" v-model="dataForm.validNum" placeholder="有效期,单位天"></el-input>
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
          couponMoney: '',
          couponTermMoney: '0',
          createTime: '',
          validNum: '',
          status: '',
          title:''
        },
        dataRule: {
          couponMoney: [
            { required: true, message: '优惠券金额不能为空', trigger: 'blur' }
          ],
          couponTermMoney: [
            { required: true, message: '优惠券满足使用条件金额不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '优惠券发放时间不能为空', trigger: 'blur' }
          ],
          validNum: [
            { required: true, message: '有效期,单位天不能为空', trigger: 'blur' }
          ],
          title: [
            { required: true, message: '优惠券满足使用条件金额不能为空', trigger: 'blur' }
          ]

        }
      }
    },
    methods: {
      keyupEvent(e,input){
        e.target.value=e.target.value.replace(/[^\d.]/g, '');
        e.target.value=e.target.value.replace(/\.{2,}/g, '.');
        e.target.value=e.target.value.replace(/^\./g, '0.');
        e.target.value=e.target.value.replace(/^\d*\.\d*\./g, e.target.value.substring(0,e.target.value.length-1));
        e.target.value=e.target.value.replace(/^0[^\.]+/g, '0')
        e.target.value=e.target.value.replace(/^(\d+)\.(\d\d).*$/, '$1.$2')
        this.input=e.target.value
      },
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/x_coupon/xcoupon/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.couponMoney = data.xCoupon.couponMoney
                // this.dataForm.couponTermMoney = data.xCoupon.couponTermMoney
                this.dataForm.createTime =   data.xCoupon.createTime
                this.dataForm.validNum = data.xCoupon.validNum
                this.dataForm.title = data.xCoupon.title
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
              url: this.$http.adornUrl(`/x_coupon/xcoupon/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'couponMoney': this.dataForm.couponMoney,
                'couponTermMoney': this.dataForm.couponTermMoney,
                'createTime': this.formatter(this.dataForm.createTime, 'yyyy-MM-dd hh:mm:ss'),
                'validNum': this.dataForm.validNum,
                'title':this.dataForm.title
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
      formatter (thistime, fmt) {
        let $this = new Date(thistime)
        let o = {
          'M+': $this.getMonth() + 1,
          'd+': $this.getDate(),
          'h+': $this.getHours(),
          'm+': $this.getMinutes(),
          's+': $this.getSeconds(),
          'q+': Math.floor(($this.getMonth() + 3) / 3),
          'S': $this.getMilliseconds()
        }
        if (/(y+)/.test(fmt)) {
          fmt = fmt.replace(RegExp.$1, ($this.getFullYear() + '').substr(4 - RegExp.$1.length))
        }
        for (var k in o) {
          if (new RegExp('(' + k + ')').test(fmt)) {
            fmt = fmt.replace(RegExp.$1, (RegExp.$1.length === 1) ? (o[k]) : (('00' + o[k]).substr(('' + o[k]).length)))
          }
        }
        return fmt
      },
    }
  }
</script>
