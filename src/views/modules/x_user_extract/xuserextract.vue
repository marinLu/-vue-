<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item label="打款状态">
        <el-select v-model="dataForm.status" placeholder="请选择">
          <el-option label="全部" value="-2"></el-option>
          <el-option label="确认打款" value="1"></el-option>
          <el-option label="拒绝打款" value="-1"></el-option>
          <el-option label="待审核" value="0"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.shopName" placeholder="商店名称" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.mobile" placeholder="电话" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.realName" placeholder="开户人" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
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
        label="ID">
      </el-table-column>
      <el-table-column
        prop="realName"
        header-align="center"
        align="center"
        label="开户人">
      </el-table-column>
      <el-table-column
        prop="openBank"
        header-align="center"
        align="center"
        label="开户行">
      </el-table-column>
      <el-table-column
        prop="cardNumber"
        header-align="center"
        align="center"
        label="卡号">
      </el-table-column>
      <el-table-column
        prop="shopName"
        header-align="center"
        align="center"
        label="店铺">
      </el-table-column>
      <el-table-column
        prop="shopMobile"
        header-align="center"
        align="center"
        label="电话">
      </el-table-column>
      <el-table-column
        prop="extractMoney"
        header-align="center"
        align="center"
        label="提现金额">
      </el-table-column>
      <el-table-column
        prop="createTime"
        header-align="center"
        align="center"
        label="提现时间">
      </el-table-column>
      <el-table-column
        prop="status"
        header-align="center"
        align="center"
        label="打款状态">
        <template slot-scope="scope">
          {{formatStatus(scope.row.status)}}
        </template>
      </el-table-column>
      <el-table-column
        prop="reviewTime"
        header-align="center"
        align="center"
        label="审核时间">
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
       <!--   <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">修改</el-button>-->
          <el-button type="text" size="small" @click="handleTypeStatus(scope.row,1)" v-if="scope.row.status==0">确认打款</el-button>
          <el-button type="text" size="small" @click="handleTypeStatus(scope.row,-1)" v-if="scope.row.status==0">拒绝打款</el-button>
        </template>
      </el-table-column>
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
  </div>
</template>

<script>
  import AddOrUpdate from './xuserextract-add-or-update'
  export default {
    data () {
      return {
        dataForm: {
          shopName: '',
          realName:'',
          mobile:'',
          status:'-2'
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false
      }
    },
    components: {
      AddOrUpdate
    },
    activated () {
      this.getDataList()
    },
    methods: {
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/x_user_extract/xuserextract/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'shopName': this.dataForm.shopName,
            'status':this.dataForm.status,
            'mobile':this.dataForm.mobile,
            'realName':this.dataForm.realName
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
      handleTypeStatus(row,status){
        this.$confirm('确认' + this.formatStatus2(status) + '该记录吗?', '提示', {
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl(`/x_user_extract/xuserextract/${!row.id ? 'save' : 'update'}`),
            method: 'post',
            data: this.$http.adornData({
              'id': row.id,
              'status':status
            })
          }).then(({data}) => {
            if (data && data.code === 0) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500,
                onClose: () => {
                  this.visible = false
                  this.getDataList()
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
        }).catch(() => {
        })
      },
      formatStatus(val) {
        switch (val) {
          case 1:
            return '确认打款'
          case -1:
            return '拒绝打款'
          case 0:
            return '待审核'
        }
      },
      formatStatus2(val) {
        switch (val) {
          case -1:
            return '拒绝打款'
          case 1:
            return '确认打款'
        }
      }
    }
  }
</script>
