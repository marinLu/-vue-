<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="会员卡标题" prop="title">
        <el-input v-model="dataForm.title"
                  placeholder="会员卡标题"></el-input>
      </el-form-item>
      <el-form-item label="会员价格" prop="price">
        <el-input v-model="dataForm.price"
                  oninput="value=value.replace(/[^\d.]/g,'')"
                  placeholder="会员价格"></el-input>
      </el-form-item>
      <el-form-item label="会员原价" prop="originalPrice">
        <el-input v-model="dataForm.originalPrice"
                  oninput="value=value.replace(/[^\d.]/g,'')"
                  placeholder="会员原价"></el-input>
      </el-form-item>
      <el-form-item label="会员大图介绍" prop="vipDetail">
        <el-upload
          class="avatar-uploader"
          :action="url"
          :before-upload="beforeUploadHandle"
          :on-success="successHandle"
          multiple
          :show-file-list="false"
        >
          <img v-if="dataForm.vipDetail" :src="dataForm.vipDetail" class="avatar"  style="max-height: 200px;max-width: 200px">
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>
      </el-form-item>
    <el-form-item label="是否限制购买" prop="isLimitBuy">
      <el-select v-model="dataForm.isLimit" placeholder="请选择" @change="changeLimitBuy">
        <el-option
          v-for="item in isLimitList"
          :key="item.id"
          :label="item.name"
          :value="item.id">
        </el-option>
      </el-select>
    </el-form-item>
      <el-form-item v-show="!limitVisible" label="限制购买次数" prop="isLimitBuy">
        <el-input v-model="dataForm.isLimitBuy"
                  placeholder="填写限制购买次数"
                  oninput="value=value.replace(/[^\d]/g,'')"></el-input>
      </el-form-item>
      <el-form-item label="使用限制" prop="isSingle">
        <el-select v-model="dataForm.isSingle" placeholder="请选择" @change="changeSingle">
          <el-option
            v-for="item in isSingleList"
            :key="item.id"
            :label="item.name"
            :value="item.id">
          </el-option>
        </el-select>
      </el-form-item>
    <el-form-item v-show="limitVisible" label="有效期,单位(月)" prop="validTime">
      <el-input v-model="dataForm.validTime" placeholder="有效期,单位(月)"></el-input>
    </el-form-item>
      <el-form-item label="是否返利" prop="isRebate">
        <el-select v-model="dataForm.isRebate" placeholder="请选择">
          <el-option
            v-for="item in isRebateList"
            :key="item.id"
            :label="item.name"
            :value="item.id">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="折扣(例:95折)" prop="discount">
        <el-input v-model="dataForm.discount" placeholder="折扣(例:95折)"></el-input>
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
        url: '',
        visible: false,
        limitVisible: true,
        singleVisible: false,
        dataForm: {
          id: 0,
          title: '',
          price: '',
          originalPrice: '',
          vipDetail: '',
          validTime: '',
          discount: '',
          isLimit: '',
          isLimitBuy: '',
          isSingle: '',
          isRebate: ''
        },
        isLimitList: [
          {id: -1, name: '不限制购买'},
          {id: 1, name: '限制购买'}
        ],
        isSingleList: [
          {id: -1, name: '时限内使用'},
          {id: 1, name: '单次使用'}
        ],
        isRebateList: [
          {id: -1, name: '不返利'},
          {id: 1, name: '返利'}
        ],
        dataRule: {
          title: [
            { required: true, message: '标题不能为空', trigger: 'blur' }
          ],
          price: [
            { required: true, message: '会员价格不能为空', trigger: 'blur' }
          ],
          originalPrice: [
            { required: true, message: '会员原价不能为空', trigger: 'blur' }
          ],
          vipDetail: [
            { required: true, message: '会员大图介绍不能为空', trigger: 'blur' }
          ],
          validTime: [
            { required: true, message: '有效期,单位(月)不能为空', trigger: 'blur' }
          ],
          isLimitList: [
            { required: true, message: '请选择是否限制购买', trigger: 'blur' }
          ],
          isSingle: [
            { required: true, message: '请选择使用限制', trigger: 'blur' }
          ],
          isRebate: [
            { required: true, message: '请选择返利与否', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/x_vip/xvip/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.title = data.xVip.title
                this.dataForm.price = data.xVip.price
                this.dataForm.originalPrice = data.xVip.originalPrice
                this.dataForm.vipDetail = data.xVip.vipDetail
                this.dataForm.validTime = data.xVip.validTime
                this.dataForm.discount = data.xVip.discount
                this.dataForm.isRebate = data.xVip.isRebate
                let isLimitBuy = data.xVip.isLimitBuy
                if (isLimitBuy === -1) {
                  this.dataForm.isLimit = -1
                  this.dataForm.isLimitBuy = -1
                  this.limitVisible = true
                } else {
                  this.dataForm.isLimit = 1
                  this.dataForm.isLimitBuy = data.xVip.isLimitBuy
                  this.limitVisible = false
                }
                let isSingle = data.xVip.isSingle
                this.dataForm.isSingle = isSingle
                if (isSingle === 1) {
                  this.singleVisible = false
                } else {
                  this.singleVisible = true
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
              url: this.$http.adornUrl(`/x_vip/xvip/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'title': this.dataForm.title,
                'price': this.dataForm.price,
                'originalPrice': this.dataForm.originalPrice,
                'vipDetail': this.dataForm.vipDetail,
                'validTime': this.dataForm.validTime,
                'discount': this.dataForm.discount,
                'isLimitBuy': this.dataForm.isLimitBuy,
                'isSingle': this.dataForm.isSingle,
                'isRebate': this.dataForm.isRebate
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
        console.log('上传返回结果' + response.url)
        if (response && response.code === 0) {
          this.dataForm.vipDetail = response.url
        } else {
          this.$message.error(response.msg)
        }
      },
      changeLimitBuy (value) {
        this.dataForm.isLimitBuy = value
        if (value === -1) {
          this.limitVisible = true
        } else {
          this.limitVisible = false
        }
      },
      changeSingle (value) {
        if (value === 1) {
          this.singleVisible = false
          this.dataForm.validTime = 0
        } else {
          this.singleVisible = true
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
