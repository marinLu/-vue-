<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="用户" >
        <el-select v-model="dataForm.userId" autocomplete="off" filterable style="width:300px">
          <el-option v-for="(item,index) in userList" :key="index" :label="item.nickName" :value="item.id">
          </el-option>
        </el-select>
      </el-form-item>
    <el-form-item label="用户名" prop="realName">
      <el-input v-model="dataForm.realName" placeholder="用户名"></el-input>
    </el-form-item>
    <el-form-item label="银行卡号" prop="cardNumber">
      <el-input v-model="dataForm.cardNumber" placeholder="银行卡号"></el-input>
    </el-form-item>
<!--    <el-form-item label="开户行logo" prop="bankLogo">
      <el-input v-model="dataForm.bankLogo" placeholder="开户行logo"></el-input>
    </el-form-item>-->
    <el-form-item label="开户行" prop="openBank">
      <el-input v-model="dataForm.openBank" placeholder="开户行"></el-input>
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
        userList:[],
        dataForm: {
          id: 0,
          userId: '',
          realName: '',
          cardNumber: '',
          bankLogo: '',
          openBank: '',
          createTime: ''
        },
        dataRule: {
          userId: [
            { required: true, message: '用户id不能为空', trigger: 'blur' }
          ],
          realName: [
            { required: true, message: '用户名不能为空', trigger: 'blur' }
          ],
          cardNumber: [
            { required: true, message: '银行卡号不能为空', trigger: 'blur' }
          ],
          openBank: [
            { required: true, message: '开户行不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.$http({
          url: this.$http.adornUrl('/x_shop/xshop/getUserList'),
          method: 'get',
          params: this.$http.adornParams({})
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.userList = data.list
          } else {
            this.userList = []
          }
        })
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/x_user_bank_card/xuserbankcard/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.userId = data.xUserBankCard.userId
                this.dataForm.realName = data.xUserBankCard.realName
                this.dataForm.cardNumber = data.xUserBankCard.cardNumber
                // this.dataForm.bankLogo = data.xUserBankCard.bankLogo
                this.dataForm.openBank = data.xUserBankCard.openBank
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
              url: this.$http.adornUrl(`/x_user_bank_card/xuserbankcard/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'userId': this.dataForm.userId,
                'realName': this.dataForm.realName,
                'cardNumber': this.dataForm.cardNumber,
                // 'bankLogo': this.dataForm.bankLogo,
                'openBank': this.dataForm.openBank
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
