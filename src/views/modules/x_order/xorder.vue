<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.orderNum" placeholder="订单号" clearable></el-input>
      </el-form-item>
      <el-form-item label="支付状态">
        <el-select v-model="dataForm.status" placeholder="请选择">
          <el-option label="全部" value=""></el-option>
          <el-option label="已支付" value="1"></el-option>
          <el-option label="未支付" value="0"></el-option>
          <el-option label="已取消" value="-1"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
      </el-form-item>
      <el-form-item>
        <span style="color: red">实付总额: {{this.totalPrice}} 元</span>
      </el-form-item>
      <el-form-item>
        <span style="color: red">钱包总额: {{this.totalWalletPrice}} 元</span>
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
        label="订单id">
      </el-table-column>
      <el-table-column
        prop="orderNum"
        header-align="center"
        align="center"
        label="订单号">
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
        prop="orderPrice"
        header-align="center"
        align="center"
        label="订单金额">
      </el-table-column>
      <el-table-column
        prop="payPrice"
        header-align="center"
        align="center"
        label="支付金额">
      </el-table-column>
      <el-table-column
        prop="status"
        header-align="center"
        align="center"
        label="支付状态">
        <template slot-scope="scope">
          {{formatStatus(scope.row.status)}}
        </template>
      </el-table-column>
      <el-table-column
        prop="coupon"
        header-align="center"
        align="center"
        label="优惠券金额">
      </el-table-column>
      <el-table-column
        prop="wallet"
        header-align="center"
        align="center"
        label=" 使用钱包金额">
      </el-table-column>
<!--      <el-table-column
        prop="payTime"
        header-align="center"
        align="center"
        label="支付时间">
      </el-table-column>-->
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
          <el-button type="text" size="small" @click="orderDetail(scope.row.id)">订单详情</el-button>
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
    <el-dialog center title="订单详情" :visible.sync="dialogFormVisible" @close='closeDialog'>
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
              prop="createTime"
              header-align="center"
              align="center"
              label="创建时间">
            </el-table-column>
          </el-table>
          <el-pagination
            @size-change="sizeChangeHandle2"
            @current-change="currentChangeHandle2"
            :current-page="detaildataForm.pageIndex"
            :page-sizes="[10, 20, 50, 100]"
            :page-size="detaildataForm.pageSize"
            :total="detaildataForm.totalPage"
            layout="total, sizes, prev, pager, next, jumper">
          </el-pagination>
        </div>
      </template>
    </el-dialog>
  </div>
</template>

<script>
  import AddOrUpdate from './xorder-add-or-update'
  export default {
    data () {
      return {
        totalPrice:'',
        totalWalletPrice:'',
        dataForm: {
          orderNum: '',
          status:''
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        detaildataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false,
        dialogFormVisible:false,
        detaildataList:[],
        detaildataForm:{
          id:'',
          pageIndex:1,
          pageSize: 10,
          totalPage: 0,
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
        this.dialogFormVisible = false
        this.detaildataForm.id = ''
      },
      orderDetail(id){
        this.detaildataForm.id = id
        this.dialogFormVisible = true
        this.getDetailDataList();
      },
      getDetailDataList () {
        this.detaildataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/x_order_detail/xorderdetail/list'),
          method: 'get',
          params: this.$http.adornParams({
            'id': this.detaildataForm.id,
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.detaildataList = data.page.list
            this.detaildataForm.totalPage = data.page.totalCount
          } else {
            this.detaildataList = []
            this.detaildataForm.totalPage = 0
          }
          this.detaildataListLoading = false
        })
      },
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/x_order/xorder/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'orderNum': this.dataForm.orderNum,
            'status':this.dataForm.status
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.dataList = data.page.list
            this.totalPage = data.page.totalCount
            this.totalPrice = data.totalPrice
            this.totalWalletPrice = data.totalWalletPrice
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
      sizeChangeHandle2 (val) {
        this.detaildataForm.pageSize = val
        this.detaildataForm.pageIndex = 1
        this.getDetailDataList()
      },
      // 当前页
      currentChangeHandle2 (val) {
        this.detaildataForm.pageIndex = val
        this.getDetailDataList()
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
      formatStatus(val) {
        switch (val) {
          case 0:
            return '未支付'
          case 1:
            return '已支付'
          case -1:
              return '已取消'
          case 2:
            return '已完成'
        }
      }
    }
  }
</script>
