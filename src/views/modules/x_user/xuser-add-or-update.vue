<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="昵称" prop="nickName">
      <el-input v-model="dataForm.nickName" placeholder="昵称"></el-input>
    </el-form-item>
    <el-form-item label="头像" prop="avatar">
      <el-input v-model="dataForm.avatar" placeholder="头像"></el-input>
    </el-form-item>
    <el-form-item label="手机号" prop="mobile">
      <el-input v-model="dataForm.mobile" placeholder="手机号"></el-input>
    </el-form-item>
    <el-form-item label="城市" prop="city">
      <el-input v-model="dataForm.city" placeholder="城市"></el-input>
    </el-form-item>
    <el-form-item label="openId" prop="openId">
      <el-input v-model="dataForm.openId" placeholder="openId"></el-input>
    </el-form-item>
    <el-form-item label="union_id" prop="unionId">
      <el-input v-model="dataForm.unionId" placeholder="union_id"></el-input>
    </el-form-item>
    <el-form-item label="个人二维码" prop="mCode">
      <el-input v-model="dataForm.mCode" placeholder="个人二维码"></el-input>
    </el-form-item>
    <el-form-item label="" prop="sessionKey">
      <el-input v-model="dataForm.sessionKey" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="创建时间" prop="createTime">
      <el-input v-model="dataForm.createTime" placeholder="创建时间"></el-input>
    </el-form-item>
    <el-form-item label="更新时间" prop="updateTime">
      <el-input v-model="dataForm.updateTime" placeholder="更新时间"></el-input>
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
          nickName: '',
          avatar: '',
          mobile: '',
          city: '',
          openId: '',
          unionId: '',
          mCode: '',
          sessionKey: '',
          createTime: '',
          updateTime: ''
        },
        dataRule: {
          nickName: [
            { required: true, message: '昵称不能为空', trigger: 'blur' }
          ],
          avatar: [
            { required: true, message: '头像不能为空', trigger: 'blur' }
          ],
          mobile: [
            { required: true, message: '手机号不能为空', trigger: 'blur' }
          ],
          city: [
            { required: true, message: '城市不能为空', trigger: 'blur' }
          ],
          openId: [
            { required: true, message: 'openId不能为空', trigger: 'blur' }
          ],
          unionId: [
            { required: true, message: 'union_id不能为空', trigger: 'blur' }
          ],
          mCode: [
            { required: true, message: '个人二维码不能为空', trigger: 'blur' }
          ],
          sessionKey: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '创建时间不能为空', trigger: 'blur' }
          ],
          updateTime: [
            { required: true, message: '更新时间不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/x_user/xuser/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.nickName = data.xUser.nickName
                this.dataForm.avatar = data.xUser.avatar
                this.dataForm.mobile = data.xUser.mobile
                this.dataForm.city = data.xUser.city
                this.dataForm.openId = data.xUser.openId
                this.dataForm.unionId = data.xUser.unionId
                this.dataForm.mCode = data.xUser.mCode
                this.dataForm.sessionKey = data.xUser.sessionKey
                this.dataForm.createTime = data.xUser.createTime
                this.dataForm.updateTime = data.xUser.updateTime
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
              url: this.$http.adornUrl(`/x_user/xuser/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'nickName': this.dataForm.nickName,
                'avatar': this.dataForm.avatar,
                'mobile': this.dataForm.mobile,
                'city': this.dataForm.city,
                'openId': this.dataForm.openId,
                'unionId': this.dataForm.unionId,
                'mCode': this.dataForm.mCode,
                'sessionKey': this.dataForm.sessionKey,
                'createTime': this.dataForm.createTime,
                'updateTime': this.dataForm.updateTime
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
