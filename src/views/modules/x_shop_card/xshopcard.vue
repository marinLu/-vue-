<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item label="是否激活">
        <el-select v-model="dataForm.isOpen" placeholder="请选择">
          <el-option label="全部" value=""></el-option>
          <el-option label="未激活" value="0"></el-option>
          <el-option label="已激活" value="1"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="使用情况">
        <el-select v-model="dataForm.status" placeholder="请选择">
          <el-option label="全部" value=""></el-option>
          <el-option label="未使用" value="0"></el-option>
          <el-option label="已使用" value="1"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.shopName" placeholder="商店名称" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button type="primary" @click="importCard()">导入卡信息</el-button>
       <!-- <el-button v-if="isAuth('x_shop_card:xshopcard:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>-->
      <!--  <el-button v-if="isAuth('x_shop_card:xshopcard:delete')" type="danger" @click="deleteHandle()" :disabled="dataListSelections.length <= 0">批量删除</el-button>-->
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column
        prop="id"
        header-align="center"
        align="center"
        label="id">
      </el-table-column>
      <el-table-column
        prop="orderId"
        header-align="center"
        align="center"
        label="订单号">
      </el-table-column>
      <el-table-column
        prop="cardNum"
        header-align="center"
        align="center"
        label="卡号">
      </el-table-column>
      <el-table-column
        prop="password"
        header-align="center"
        align="center"
        label="密码">
      </el-table-column>
<!--      <el-table-column
        prop="shopId"
        header-align="center"
        align="center"
        label="店铺id">
      </el-table-column>-->
      <el-table-column
        prop="shopName"
        header-align="center"
        align="center"
        label="店铺名称">
      </el-table-column>
      <el-table-column
        prop="price"
        header-align="center"
        align="center"
        label="卡金额">
      </el-table-column>
      <el-table-column
        prop="isOpen"
        header-align="center"
        align="center"
        label="是否激活">
        <template slot-scope="scope">
          {{formatIsOpen(scope.row.status)}}
        </template>
      </el-table-column>
      <el-table-column
        prop="status"
        header-align="center"
        align="center"
        label="使用情况">
        <template slot-scope="scope">
          {{formatStatus(scope.row.status)}}
        </template>
      </el-table-column>
      <el-table-column
        prop="createTime"
        header-align="center"
        align="center"
        label="创建时间">
      </el-table-column>
      <el-table-column
        prop="userTime"
        header-align="center"
        align="center"
        label="使用时间">
      </el-table-column>
    <!--  <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">修改</el-button>
          <el-button type="text" size="small" @click="deleteHandle(scope.row.id)">删除</el-button>
        </template>
      </el-table-column>-->
    </el-table>
    <el-pagination
      @size-change="sizeChangeHandle"
      @current-change="currentChangeHandle"
      :current-page="pageIndex"
      :page-sizes="[10, 20, 50, 100]"
      :page-size="pageSize"
      :total="totalPage"
      layout="total, sizes, prev, pager, next, jumper">
    </el-pagination>
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
    <el-dialog center title="导入卡信息" :visible.sync="dialogFormVisible" @close='closeDialog' style="width: 1200px">
      <el-form>
        <el-upload
          class="upload-demo"
          accept="xlsx"
          drag
          :action="url"
          :before-upload="beforeUploadHandle"
          :on-success="successHandle"
          multiple>
          <i class="el-icon-upload"></i>
          <div class="el-upload__text">将文件拖到此处，或<em>点击上传</em></div>
        </el-upload>
      </el-form>
<!--      <span slot="footer" class="dialog-footer">
      <el-button @click='closeDialog()'>取消</el-button>
      <el-button type="primary" @click="saveCard()">确定</el-button>
    </span>-->
    </el-dialog>
  </div>
</template>

<script>
  import AddOrUpdate from './xshopcard-add-or-update'
  export default {
    data () {
      return {
        url:'',
        dataForm: {
          status: '',
          shopName:'',
          isOpen:''
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false,
        dialogFormVisible:false
      }
    },
    components: {
      AddOrUpdate
    },
    activated () {
      this.getDataList()
    },
    methods: {
      importCard(){
        this.url = this.$http.adornUrl(`/x_shop_card/xshopcard/readExcel?token=${JSON.parse(sessionStorage.getItem('token'))}`)
        this.dialogFormVisible=true
      },
      saveCard(){
        console.log( this.url )
      },
      closeDialog(){
        this.dialogFormVisible=false
      },
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/x_shop_card/xshopcard/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'status': this.dataForm.status,
            'shopName':this.dataForm.shopName,
            'isOpen':this.dataForm.isOpen
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.dataList = data.page.list
            this.totalPage = data.page.totalCount
          } else {
            this.dataList = []
            this.totalPage = 0
          }
          this.dataListLoading = false
        })
      },
      // 每页数
      sizeChangeHandle (val) {
        this.pageSize = val
        this.pageIndex = 1
        this.getDataList()
      },
      // 当前页
      currentChangeHandle (val) {
        this.pageIndex = val
        this.getDataList()
      },
      // 多选
      selectionChangeHandle (val) {
        this.dataListSelections = val
      },
      // 新增 / 修改
      addOrUpdateHandle (id) {
        this.addOrUpdateVisible = true
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init(id)
        })
      },
      // 删除
      deleteHandle (id) {
        var ids = id ? [id] : this.dataListSelections.map(item => {
          return item.id
        })
        this.$confirm(`确定对[id=${ids.join(',')}]进行[${id ? '删除' : '批量删除'}]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/x_shop_card/xshopcard/delete'),
            method: 'post',
            data: this.$http.adornData(ids, false)
          }).then(({data}) => {
            if (data && data.code === 0) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500,
                onClose: () => {
                  this.getDataList()
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
        })
      },
      // 上传之前
      beforeUploadHandle (file) {
        console.log(file.type)
        if (file.type !== 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet' ) {
          this.$message.error('只支持xlsx格式的文件！')
          return false
        }
      },
      // 上传成功
      successHandle (response) {
        console.log("上传返回结果"+response)
        if (response && response.code === 0) {
          this.$message.success(response.msg)
          this.dialogFormVisible=false
        } else {
          this.$message.error(response.msg)
        }
      },
      formatStatus(val){
        switch (val) {
            case 0:
              return '未使用'
             case 1:
            return '已使用'
        }
      },
      formatIsOpen(val){
        switch (val) {
          case 0:
            return '未激活'
          case 1:
            return '已激活'
        }
      }
    }
  }
</script>
