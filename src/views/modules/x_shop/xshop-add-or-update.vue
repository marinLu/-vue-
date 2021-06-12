<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="150px">
      <el-form-item label="店铺管理员" >
        <el-select v-model="dataForm.userId" autocomplete="off" filterable style="width:300px">
          <el-option v-for="(item,index) in userList" :key="index" :label="item.nickName" :value="item.id">
          </el-option>
        </el-select>
      </el-form-item>
    <el-form-item label="商店名称" prop="shopName">
      <el-input v-model="dataForm.shopName" placeholder="商店名称"></el-input>
    </el-form-item>
      <el-form-item label="联系电话" prop="shopMobile">
        <el-input v-model="dataForm.shopMobile" placeholder="联系电话"></el-input>
      </el-form-item>
      <el-form-item label="商店小图" prop="shopBanner">
        <el-upload
          class="avatar-uploader"
          :action="url"
          :before-upload="beforeUploadHandle"
          :on-success="successHandle"
          multiple
          :show-file-list="false"
        >
          <img v-if="dataForm.shopBanner" :src="dataForm.shopBanner" class="avatar"  style="max-height: 200px;max-width: 200px">
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>
      </el-form-item>
      <el-form-item label="商店详情图" prop="shopDetail">
        <el-upload
          class="avatar-uploader"
          :action="url"
          :before-upload="beforeUploadHandle2"
          :on-success="successHandle2"
          multiple
          :show-file-list="false"
        >
          <img v-if="dataForm.shopDetail" :src="dataForm.shopDetail" class="avatar"  style="max-height: 200px;max-width: 200px">
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>
      </el-form-item>
    <el-form-item label="店铺地址" prop="shopAddress">
      <el-input v-model="dataForm.shopAddress" placeholder="店铺地址"></el-input>
    </el-form-item>
    <el-form-item label="地址详情" prop="addressDetail">
      <el-input v-model="dataForm.addressDetail" placeholder="地址详情"></el-input>
    </el-form-item>
    <el-form-item label="商店简介" prop="shopIntroduction">
      <el-input v-model="dataForm.shopIntroduction" placeholder="商店简介"></el-input>
    </el-form-item>
      <el-form-item label="简介补充营业执照等" prop="introductionAdd">
        <el-upload
          class="upload-demo"
          :action="url"
          :on-preview="handlePreview"
          :on-remove="handleRemove"
          :before-upload="beforeUploadHandle3"
          :on-success="successHandle3"
          :show-file-list="true"
          :file-list="dataForm.introductionAddList"
          list-type="picture-card"
         >
          <el-button size="small" type="primary">点击上传</el-button>
          <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
        </el-upload>
      </el-form-item>
    <el-form-item label="营业时间" prop="businessTime">
      <el-time-picker
        is-range
        v-model="dataForm.businessTime"
        format="HH:mm"
        value-format="HH:mm"
        range-separator="至"
        start-placeholder="开始时间"
        end-placeholder="结束时间"
        placeholder="选择时间范围">
      </el-time-picker>
    </el-form-item>
      <el-form-item label="店铺标准洗车费用" prop="shopPrice">
        <el-input v-model="dataForm.shopPrice" placeholder="店铺标准洗车费用"></el-input>
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
          url:'',
        visible: false,
        dataForm: {
          id: 0,
          shopName: '',
          shopBanner: '',
          shopDetail: '',
          shopAddress: '',
          addressDetail: '',
          shopIntroduction: '',
          introductionAddList: [],
          businessTime:'',
          introductionAdd:[],
          userId:'',
          shopMobile:'',
          shopPrice:''
        },
        userList:[],
        dataRule: {
          shopName: [
            { required: true, message: '商店名称不能为空', trigger: 'blur' }
          ],
          shopMobile: [
            { required: true, message: '联系电话不能为空', trigger: 'blur' }
          ],
          shopBanner: [
            { required: true, message: '商店小图不能为空', trigger: 'blur' }
          ],
          shopDetail: [
            { required: true, message: '商店详情图不能为空', trigger: 'blur' }
          ],
          shopAddress: [
            { required: true, message: '店铺地址不能为空', trigger: 'blur' }
          ],
          addressDetail: [
            { required: true, message: '地址详情不能为空', trigger: 'blur' }
          ],
          shopIntroduction: [
            { required: true, message: '商店简介不能为空', trigger: 'blur' }
          ],
          introductionAdd: [
            { required: true, message: '简介补充营业执照等不能为空', trigger: 'blur' }
          ],
          businessTime: [
            { required: true, message: '营业时间不能为空', trigger: 'blur' }
          ],
          shopPrice: [
            { required: true, message: '店铺标准洗车费用不能为空', trigger: 'blur' }
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
        this.url = this.$http.adornUrl(`/sys/oss/upload?token=${JSON.parse(sessionStorage.getItem('token'))}`)
        this.dataForm.id = id || 0
        this.visible = true
        this.dataForm.introductionAddList = []
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/x_shop/xshop/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.shopName = data.xShop.shopName
                this.dataForm.shopBanner = data.xShop.shopBanner
                this.dataForm.shopDetail = data.xShop.shopDetail
                this.dataForm.shopAddress = data.xShop.shopAddress
                this.dataForm.addressDetail = data.xShop.addressDetail
                this.dataForm.shopIntroduction = data.xShop.shopIntroduction
                this.dataForm.userId = data.xShop.userId
                this.dataForm.shopMobile = data.xShop.shopMobile
                this.dataForm.shopPrice = data.xShop.shopPrice
                this.dataForm.businessTime = data.xShop.businessTime.split("-")
                var intro = data.xShop.introductionAdd.split(",");
                if (intro.length>0){
                  for (let i = 0; i < intro.length; i++) {
                    var data = {
                      name:'简介',
                      url:intro[i]
                    }
                    this.dataForm.introductionAddList.push(data)
                    this.dataForm.introductionAdd.push(intro[i])
                  }
                }
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
              url: this.$http.adornUrl(`/x_shop/xshop/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'shopName': this.dataForm.shopName,
                'shopBanner': this.dataForm.shopBanner,
                'shopDetail': this.dataForm.shopDetail,
                'shopAddress': this.dataForm.shopAddress,
                'addressDetail': this.dataForm.addressDetail,
                'shopIntroduction': this.dataForm.shopIntroduction,
                'introductionAdd': this.dataForm.introductionAdd.toString(),
                'businessTime': this.dataForm.businessTime[0]+"-"+this.dataForm.businessTime[1],
                'userId':this.dataForm.userId,
                'shopMobile':this.dataForm.shopMobile,
                'shopPrice': this.dataForm.shopPrice
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.dataForm.introductionAddList=[]
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
          this.dataForm.shopBanner = response.url;
        } else {
          this.$message.error(response.msg)
        }
      },
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
          this.dataForm.shopDetail = response.url;
        } else {
          this.$message.error(response.msg)
        }
      },
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
          // this.dataForm.introductionAdd = response.url;
          var data = {}
          data.name = '简介'
          data.url=response.url
          this.dataForm.introductionAddList.push(data)
          this.dataForm.introductionAdd.push(response.url)
        } else {
          this.$message.error(response.msg)
        }
      },
      handleRemove(file, fileList) {
        console.log(file.url, fileList);
        for (const i in this.dataForm.introductionAdd) {
          if (this.dataForm.introductionAdd[i] === file.url) {
            this.dataForm.introductionAdd.splice(i, 1)
          }
        }
        console.log(this.dataForm.introductionAdd)
      },
      handlePreview(file) {
        console.log(file);
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
