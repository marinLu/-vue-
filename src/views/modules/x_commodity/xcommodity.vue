<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.commodityName" placeholder="商品名称" clearable></el-input>
      </el-form-item>
      <el-form-item label="状态">
        <el-select v-model="dataForm.status" placeholder="请选择">
          <el-option label="全部" value=""></el-option>
          <el-option label="上线" value="1"></el-option>
          <el-option label="下线" value="-1"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="isAuth('x_commodity:xcommodity:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
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
        label="商品id">
      </el-table-column>
      <el-table-column
        prop="commodityName"
        header-align="center"
        align="center"
        label="商品名称">
      </el-table-column>
      <el-table-column
        prop="commodityBanner"
        header-align="center"
        align="center"
        label="图片">
        <template slot-scope="scope">
          <el-popover placement="left" title="" trigger="hover">
            <img :src="scope.row.commodityBanner" style="max-height: 200px;max-width: 200px"/>
            <img slot="reference" :src="scope.row.commodityBanner"
                 :alt="scope.row.commodityBanner" style="max-height: 50px;max-width: 50px">
          </el-popover>
        </template>
      </el-table-column>
      <el-table-column
        prop="commodityDetail"
        header-align="center"
        align="center"
        label="详情图片">
        <template slot-scope="scope">
          <el-popover placement="left" title="" trigger="hover">
            <img :src="scope.row.commodityDetail" style="max-height: 200px;max-width: 200px"/>
            <img slot="reference" :src="scope.row.commodityDetail"
                 :alt="scope.row.commodityDetail" style="max-height: 50px;max-width: 50px">
          </el-popover>
        </template>
      </el-table-column>
      <el-table-column
        prop="commodityPrice"
        header-align="center"
        align="center"
        label="商品价格">
      </el-table-column>
      <el-table-column
        prop="commodityDiscountPrice"
        header-align="center"
        align="center"
        label="商品折扣价格">
      </el-table-column>
      <el-table-column
        prop="commodifyNum"
        header-align="center"
        align="center"
        label="商品销量">
      </el-table-column>
      <el-table-column
        prop="status"
        header-align="center"
        align="center"
        label="状态">
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
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">修改</el-button>
          <el-button type="text" size="small" @click="deleteHandle(scope.row.id)">删除</el-button>
          <el-button type="text" size="small" @click="handleTypeStatus(scope.row)" v-if="scope.row.status==1">下线</el-button>
          <el-button type="text" size="small" @click="handleTypeStatus(scope.row)" v-else-if="scope.row.status==-1">上线</el-button>
          <el-button type="text" size="small" @click="orderDetail(scope.row.id)">销售明细</el-button>
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
    <el-dialog center title="销售明细" :visible.sync="dialogFormVisible2" @close='closeDialog'>
      <template>
        <div class="mod-config">
          <el-table
            :data="detaildataList"
            border
            v-loading="detaildataListLoading"
            style="width: 100%;">
            <el-table-column
              prop="orderId"
              header-align="center"
              align="center"
              label="订单号">
            </el-table-column>
            <el-table-column
              prop="commodityName"
              header-align="center"
              align="center"
              label="商品名称">
            </el-table-column>
            <el-table-column
              prop="commodityPrice"
              header-align="center"
              align="center"
              label="商品价格">
            </el-table-column>
            <el-table-column
              prop="commodityNum"
              header-align="center"
              align="center"
              label="商品购买数量">
            </el-table-column>
            <el-table-column
              prop="commodityBanner"
              header-align="center"
              align="center"
              label="商品预览图">
              <template slot-scope="scope">
                <el-popover placement="left" title="" trigger="hover">
                  <img :src="scope.row.commodityBanner" style="max-height: 200px;max-width: 200px"/>
                  <img slot="reference" :src="scope.row.commodityBanner"
                       :alt="scope.row.commodityBanner" style="max-height: 50px;max-width: 50px">
                </el-popover>
              </template>
            </el-table-column>
            <el-table-column
              prop="nickName"
              header-align="center"
              align="center"
              label="昵称">
            </el-table-column>
            <el-table-column
              prop="avatar"
              header-align="center"
              align="center"
              label="头像">
              <template slot-scope="scope">
                <el-popover placement="left" title="" trigger="hover">
                  <img :src="scope.row.avatar" style="max-height: 200px;max-width: 200px"/>
                  <img slot="reference" :src="scope.row.avatar"
                       :alt="scope.row.avatar" style="max-height: 50px;max-width: 50px">
                </el-popover>
              </template>
            </el-table-column>
            <el-table-column
              prop="carModel"
              header-align="center"
              align="center"
              label="车品牌">
            </el-table-column>
            <el-table-column
              prop="licensePlate"
              header-align="center"
              align="center"
              label="车牌号">
            </el-table-column>
            <el-table-column
              prop="carBelong"
              header-align="center"
              align="center"
              label="所属人">
            </el-table-column>
            <el-table-column
              prop="createTime"
              header-align="center"
              align="center"
              label="创建时间">
            </el-table-column>
          </el-table>
          <el-pagination
            @size-change="sizeChangeHandle3"
            @current-change="currentChangeHandle3"
            :current-page="detaildataForm.pageIndex3"
            :page-sizes="[10, 20, 50, 100]"
            :page-size="detaildataForm.pageSize3"
            :total="detaildataForm.totalPage3"
            layout="total, sizes, prev, pager, next, jumper">
          </el-pagination>
        </div>
      </template>
    </el-dialog>
  </div>
</template>

<script>
  import AddOrUpdate from './xcommodity-add-or-update'
  export default {
    data () {
      return {
        dataForm: {
          status: '',
          commodityName:''
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false,
        dialogFormVisible2:false,
        detaildataList:[],
        detaildataListLoading:false,
        detaildataForm:{
          commodityId:'',
          pageIndex3:1,
          pageSize3: 10,
          totalPage3: 0,
        }
      }
    },
    components: {
      AddOrUpdate
    },
    activated () {
      this.getDataList()
    },
    methods: {
      closeDialog(){
        this.dialogFormVisible2 = false
        this.detaildataForm.commodityId = ''
      },
      orderDetail(id){
        this.detaildataForm.commodityId = id
        this.dialogFormVisible2 = true
        this.orderDetailData()
      },
      orderDetailData(){
        this.detaildataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/x_order_detail/xorderdetail/listDetail'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.detaildataForm.pageIndex3,
            'limit': this.detaildataForm.pageSize3,
            'commodityId': this.detaildataForm.commodityId,
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.detaildataList = data.page.list
            this.detaildataForm.totalPage3 = data.page.totalCount
          } else {
            this.detaildataList = []
            this.detaildataForm.totalPage3 = 0
          }
          this.detaildataListLoading = false
        })
      },
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/x_commodity/xcommodity/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'status': this.dataForm.status,
            'commodityName':this.dataForm.commodityName
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
      sizeChangeHandle3 (val) {
        this.detaildataForm.pageSize3 = val
        this.detaildataForm.pageIndex3 = 1
        this.orderDetailData()
      },
      // 当前页
      currentChangeHandle3 (val) {
        this.detaildataForm.pageIndex3 = val
        this.orderDetailData()
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
            url: this.$http.adornUrl('/x_commodity/xcommodity/delete'),
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
      handleTypeStatus(row){
        this.$confirm('确认' + this.formatStatus2(row.status) + '该banner吗?', '提示', {
          type: 'warning'
        }).then(() => {
          var status;
          switch (row.status) {
            case 1:
              status = -1
              break
            case -1:
              status = 1
              break
          }
          this.$http({
            url: this.$http.adornUrl(`/x_commodity/xcommodity/${!row.id ? 'save' : 'update'}`),
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
            return '上线'
          case -1:
            return '下线'
        }
      },
      formatStatus2(val) {
        switch (val) {
          case 1:
            return '下线'
          case -1:
            return '上线'
        }
      }
    }
  }
</script>
