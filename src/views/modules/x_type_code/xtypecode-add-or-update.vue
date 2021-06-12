<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="code" prop="code">
      <el-input v-model="dataForm.code" placeholder="code"></el-input>
    </el-form-item>
    <el-form-item label="上级code" prop="subCode">
      <el-input v-model="dataForm.subCode" placeholder="上级code"></el-input>
    </el-form-item>
    <el-form-item label="标题名称" prop="title">
      <el-input v-model="dataForm.title" placeholder="标题名称"></el-input>
    </el-form-item>
    <el-form-item label="状态,1上线,-1下线" prop="status">
      <el-input v-model="dataForm.status" placeholder="状态,1上线,-1下线"></el-input>
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
          code: '',
          subCode: '',
          title: '',
          status: ''
        },
        dataRule: {
          code: [
            { required: true, message: 'code不能为空', trigger: 'blur' }
          ],
          subCode: [
            { required: true, message: '上级code不能为空', trigger: 'blur' }
          ],
          title: [
            { required: true, message: '标题名称不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '状态,1上线,-1下线不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/x_type_code/xtypecode/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.code = data.xTypeCode.code
                this.dataForm.subCode = data.xTypeCode.subCode
                this.dataForm.title = data.xTypeCode.title
                this.dataForm.status = data.xTypeCode.status
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
              url: this.$http.adornUrl(`/x_type_code/xtypecode/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'code': this.dataForm.code,
                'subCode': this.dataForm.subCode,
                'title': this.dataForm.title,
                'status': this.dataForm.status
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
