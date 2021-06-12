<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="120px">
    <el-form-item label="标题名称" prop="title">
      <el-input v-model="dataForm.title" placeholder="标题名称"></el-input>
    </el-form-item>
      <el-form-item label="副标题" prop="subTitle">
        <el-input v-model="dataForm.subTitle" placeholder="副标题"></el-input>
      </el-form-item>
      <el-form-item label="预计节省金额" prop="thriftMoney">
        <el-input v-model="dataForm.thriftMoney" placeholder="预计节省金额"></el-input>
      </el-form-item>
      <el-form-item label="角标描述" prop="upperDetail">
        <el-input v-model="dataForm.upperDetail" placeholder="角标描述"></el-input>
      </el-form-item>
      <el-form-item label="图标" prop="icon">
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
      <el-form-item label="会员权益icon" prop="memberIcon">
        <el-upload
          class="avatar-uploader"
          :action="url"
          :before-upload="beforeUploadHandle2"
          :on-success="successHandle2"
          multiple
          :show-file-list="false"
        >
          <img v-if="dataForm.memberIcon" :src="dataForm.memberIcon" class="avatar"  style="max-height: 200px;max-width: 200px">
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>
      </el-form-item>
      <el-form-item label="会员权益icon2" prop="vipIcon">
        <el-upload
          class="avatar-uploader"
          :action="url"
          :before-upload="beforeUploadHandle3"
          :on-success="successHandle3"
          multiple
          :show-file-list="false"
        >
          <img v-if="dataForm.vipIcon" :src="dataForm.vipIcon" class="avatar"  style="max-height: 200px;max-width: 200px">
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>
      </el-form-item>
      <el-form-item label="图片详情" prop="pictureLink">
        <el-upload
          class="avatar-uploader"
          :action="url"
          :before-upload="beforeUploadHandle4"
          :on-success="successHandle4"
          multiple
          :show-file-list="false"
        >
          <img v-if="dataForm.pictureLink" :src="dataForm.pictureLink" class="avatar"  style="max-height: 200px;max-width: 200px">
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>
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
          title: '',
          icon: '',
          memberIcon: '',
          vipIcon:'',
          pictureLink:'',
          subTitle:'',
          thriftMoney:'',
          upperDetail:''
        },
        dataRule: {
          title: [
            { required: true, message: '标题名称不能为空', trigger: 'blur' }
          ],
          icon: [
            { required: true, message: '图标不能为空', trigger: 'blur' }
          ],
          memberIcon: [
            { required: true, message: '会员权益不能为空', trigger: 'blur' }
          ],
          vipIcon: [
            { required: true, message: '会员权益icon2不能为空', trigger: 'blur' }
          ],
          pictureLink: [
            { required: true, message: '图片详情不能为空', trigger: 'blur' }
          ],
          subTitle: [
            { required: true, message: '副标题不能为空', trigger: 'blur' }
          ],
          thiriftMoney: [
            { required: true, message: '预估节省金额不能为空', trigger: 'blur' }
          ],
          upperDetail: [
            { required: true, message: '角标描述不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.url = this.$http.adornUrl(`/sys/oss/upload?token=${JSON.parse(sessionStorage.getItem('token'))}`)
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/x_type/xtype/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.title = data.xType.title
                this.dataForm.icon = data.xType.icon
                this.dataForm.memberIcon = data.xType.memberIcon
                this.dataForm.vipIcon = data.xType.vipIcon
                this.dataForm.pictureLink = data.xType.pictureLink
                this.dataForm.subTitle = data.xType.subTitle
                this.dataForm.thriftMoney = data.xType.thriftMoney
                this.dataForm.upperDetail = data.xType.upperDetail
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
              url: this.$http.adornUrl(`/x_type/xtype/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'title': this.dataForm.title,
                'icon': this.dataForm.icon,
                'memberIcon': this.dataForm.memberIcon,
                'vipIcon':this.dataForm.vipIcon,
                'pictureLink':this.dataForm.pictureLink,
                'subTitle':this.dataForm.subTitle,
                'thriftMoney':this.dataForm.thriftMoney,
                'upperDetail':this.dataForm.upperDetail
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
          this.dataForm.memberIcon = response.url;
        } else {
          this.$message.error(response.msg)
        }
      },
      // 上传之前
      beforeUploadHandle3 (file) {
        if (file.type !== 'image/jpg' && file.type !== 'image/jpeg' && file.type !== 'image/png' && file.type !== 'image/gif') {
          this.$message.error('只支持jpg、png、gif格式的图片！')
          return false
        }
        this.num++
      },
      // 上传成功
      successHandle3 (response, file) {
        console.log("上传返回结果"+response.url)
        if (response && response.code === 0) {
          this.dataForm.vipIcon = response.url;
        } else {
          this.$message.error(response.msg)
        }
      },
      // 上传之前
      beforeUploadHandle4 (file) {
        if (file.type !== 'image/jpg' && file.type !== 'image/jpeg' && file.type !== 'image/png' && file.type !== 'image/gif') {
          this.$message.error('只支持jpg、png、gif格式的图片！')
          return false
        }
        this.num++
      },
      // 上传成功
      successHandle4 (response, file) {
        console.log("上传返回结果"+response.url)
        if (response && response.code === 0) {
          this.dataForm.pictureLink = response.url;
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
