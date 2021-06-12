<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="权益icon" prop="icon">
        <el-upload
          class="avatar-uploader"
          :action="url"
          :before-upload="beforeUploadHandle"
          :on-success="successHandle"
          multiple
          :show-file-list="false"
        >
          <img v-if="dataForm.icon" :src="dataForm.icon" class="avatar"  style="max-height: 200px;max-width: 200px">
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>
      </el-form-item>
    <el-form-item label="权益名称" prop="title">
      <el-input v-model="dataForm.title" placeholder="权益名称"></el-input>
    </el-form-item>
    <el-form-item label="副标题" prop="subTitile">
      <el-input v-model="dataForm.subTitile" placeholder="副标题"></el-input>
    </el-form-item>
      <el-form-item label="详情图" prop="detailLink">
        <el-upload
          class="avatar-uploader"
          :action="url"
          :before-upload="beforeUploadHandle2"
          :on-success="successHandle2"
          multiple
          :show-file-list="false"
        >
          <img v-if="dataForm.detailLink" :src="dataForm.detailLink" class="avatar"  style="max-height: 200px;max-width: 200px">
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>
      </el-form-item>
    <el-form-item label="预计节省金额" prop="thriftMoney">
      <el-input v-model="dataForm.thriftMoney" placeholder="预计节省金额"></el-input>
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
          icon: '',
          title: '',
          subTitile: '',
          detailLink: '',
          thriftMoney: ''
        },
        dataRule: {
          icon: [
            { required: true, message: '权益icon不能为空', trigger: 'blur' }
          ],
          title: [
            { required: true, message: '权益名称不能为空', trigger: 'blur' }
          ],
          subTitile: [
            { required: true, message: '副标题不能为空', trigger: 'blur' }
          ],
          detailLink: [
            { required: true, message: '详情图不能为空', trigger: 'blur' }
          ],
          thriftMoney: [
            { required: true, message: '预计节省金额不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/x_vip_power/xvippower/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.icon = data.xVipPower.icon
                this.dataForm.title = data.xVipPower.title
                this.dataForm.subTitile = data.xVipPower.subTitile
                this.dataForm.detailLink = data.xVipPower.detailLink
                this.dataForm.thriftMoney = data.xVipPower.thriftMoney
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
              url: this.$http.adornUrl(`/x_vip_power/xvippower/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'icon': this.dataForm.icon,
                'title': this.dataForm.title,
                'subTitile': this.dataForm.subTitile,
                'detailLink': this.dataForm.detailLink,
                'thriftMoney': this.dataForm.thriftMoney
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
          this.dataForm.icon = response.url;
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
          this.dataForm.detailLink = response.url;
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
