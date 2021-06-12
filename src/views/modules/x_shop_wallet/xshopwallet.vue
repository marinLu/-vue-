<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.shopName" placeholder="商店名称" clearable></el-input>
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
        label="商店id">
      </el-table-column>
      <el-table-column
        prop="shopName"
        header-align="center"
        align="center"
        label="商店名称">
      </el-table-column>
      <el-table-column
        prop="shopMobile"
        header-align="center"
        align="center"
        label="联系电话">
      </el-table-column>
      <el-table-column
        prop="shopBanner"
        header-align="center"
        align="center"
        label="商店小图">
        <template slot-scope="scope">
          <el-popover placement="left" title="" trigger="hover">
            <img :src="scope.row.shopBanner" style="max-height: 200px;max-width: 200px"/>
            <img slot="reference" :src="scope.row.shopBanner"
                 :alt="scope.row.shopBanner" style="max-height: 50px;max-width: 50px">
          </el-popover>
        </template>
      </el-table-column>
      <el-table-column
        prop="shopDetail"
        header-align="center"
        align="center"
        label="商店详情图">
        <template slot-scope="scope">
          <el-popover placement="left" title="" trigger="hover">
            <img :src="scope.row.shopDetail" style="max-height: 200px;max-width: 200px"/>
            <img slot="reference" :src="scope.row.shopDetail"
                 :alt="scope.row.shopDetail" style="max-height: 50px;max-width: 50px">
          </el-popover>
        </template>
      </el-table-column>
      <el-table-column
        prop="shopAddress"
        header-align="center"
        align="center"
        label="店铺地址">
      </el-table-column>
      <el-table-column
        prop="addressDetail"
        header-align="center"
        align="center"
        label="地址详情">
      </el-table-column>
      <el-table-column
        prop="shopIntroduction"
        header-align="center"
        align="center"
        label="商店简介">
      </el-table-column>
      <el-table-column
        prop="introductionAdd"
        header-align="center"
        align="center"
        label="简介补充营业执照等">
        <template slot-scope="scope">
          <el-popover placement="left" title="" trigger="hover">
            <img :src="scope.row.introductionAdd" style="max-height: 200px;max-width: 200px"/>
            <img slot="reference" :src="scope.row.introductionAdd"
                 :alt="scope.row.introductionAdd" style="max-height: 50px;max-width: 50px">
          </el-popover>
        </template>
      </el-table-column>
      <el-table-column
        prop="receiveStatus"
        header-align="center"
        align="center"
        label="审核状态">
        <template slot-scope="scope">
          {{formatReceiveStatus(scope.row.receiveStatus)}}
        </template>
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
        prop="businessTime"
        header-align="center"
        align="center"
        label="营业时间">
      </el-table-column>
      <el-table-column
        prop="shopPrice"
        header-align="center"
        align="center"
        label="店铺标准洗车费用">
      </el-table-column>
      <el-table-column
        prop="wallet"
        header-align="center"
        align="center"
        label="钱包余额">
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="saveUpdate(scope.row.userId)">增加扣除</el-button>
          <el-button type="text" size="small" @click="walletDetail(scope.row.userId)">钱包明细</el-button>
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
    <el-dialog center title="钱包明细" :visible.sync="dialogFormVisible2" @close='closeDialog'>
      <template>
        <div class="mod-config">
          <el-table
            :data="detaildataList"
            border
            v-loading="detaildataListLoading"
            style="width: 100%;">
            <el-table-column
              prop="id"
              header-align="center"
              align="center"
              label="钱包明细id">
            </el-table-column>
            <el-table-column
              prop="userId"
              header-align="center"
              align="center"
              label="用户id">
            </el-table-column>
            <el-table-column
              prop="money"
              header-align="center"
              align="center"
              label="明细金额">
            </el-table-column>
            <el-table-column
              prop="type"
              header-align="center"
              align="center"
              label="状态">
              <template slot-scope="scope">
                {{formatType(scope.row.type)}}
              </template>
            </el-table-column>
            <el-table-column
              prop="costType"
              header-align="center"
              align="center"
              label="消费类型">
              <template slot-scope="scope">
                {{formatCostType(scope.row.costType)}}
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
    <el-dialog center title="增加扣除" :visible.sync="dialogFormVisible3" @close='closeDialog'>
      <el-form label-width="150px">
        <el-form-item label="增加/扣除">
          <el-radio v-model="saveUpdateData.type" label="1">增加</el-radio>
          <el-radio v-model="saveUpdateData.type" label="-1">扣除</el-radio>
        </el-form-item>
        <el-form-item label="金额" >
          <el-input v-model="saveUpdateData.money" placeholder="金额"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
      <el-button @click='closeDialog()'>取消</el-button>
      <el-button type="primary" @click="saveUpdateWallet()">确定</el-button>
    </span>
    </el-dialog>
  </div>
</template>

<script>
  export default {
    data () {
      return {
        show:false,
        dataForm: {
          shopName: '',
          status:''
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false,
        addOrUpdateServiceVisible:false,
        detaildataListLoading:false,
        detaildataForm:{
          userId:'',
          pageIndex3:1,
          pageSize3: 10,
          totalPage3: 0,
        },
        dialogFormVisible2:false,
        detaildataList:[],
        dialogFormVisible3:false,
        saveUpdateData:{
          "userId":'',
          "money":'',
          "type":'1'
        }
      }
    },
    activated () {
      this.getDataList()
    },
    methods: {
      closeDialog(){
        this.dialogFormVisible2 = false
        this.dialogFormVisible3 = false
        this.detaildataForm.id = ''
      },
      saveUpdate(userId){
        this.saveUpdateData.userId = userId
        this.dialogFormVisible3 = true
      },
      saveUpdateWallet(){
        this.$http({
          url: this.$http.adornUrl('/x_shop_wallet/xshopwallet/save'),
          method: 'post',
          data: this.$http.adornData({
            "userId": this.saveUpdateData.userId,
            "type": this.saveUpdateData.type,
            "costType":0,
            "money": this.saveUpdateData.money
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.$message({
              message: '操作成功',
              type: 'success',
              duration: 1500,
              onClose: () => {
                this.dialogFormVisible3 = false
                this.getDataList()
              }
            })
          } else {
            this.$message.error(data.msg)
          }
        })
      },
      walletDetail(userId){
        this.detaildataForm.userId = userId
        this.dialogFormVisible2 = true
        this.walletDetailData()
      },
      walletDetailData(){
        this.detaildataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/x_wallet_detail/xwalletdetail/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.detaildataForm.pageIndex3,
            'limit': this.detaildataForm.pageSize3,
            'userId': this.detaildataForm.userId,
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
      orderDetail(shopId){
        this.orderdataForm.shopId = shopId
        this.dialogFormVisible = true
        this.orderDetailData()
      },
      // 获取数据列表
      getDataList () {

        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/x_shop_wallet/xshopwallet/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'shopName': this.dataForm.shopName,
            'status':this.dataForm.status
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
        this.walletDetailData()
      },
      // 当前页
      currentChangeHandle3 (val) {
        this.detaildataForm.pageIndex3 = val
        this.walletDetailData()
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
      },
      formatReceiveStatus(val) {
        switch (val) {
          case 1:
            return '审核通过'
          case -1:
            return '审核不通过'
          case 0:
            return '待审核'
        }
      },
      formatIsDiscount(val){
        switch (val) {
          case 0:
            return '不折扣'
          case 1:
            return '折扣'
        }
      },
      formatServiceOrderStatus(val) {
        switch (val) {
          case 1:
            return '已支付'
          case 0:
            return '未支付'
        }
      },
      formatType(val) {
        switch (val) {
          case 1:
            return '增加'
          case -1:
            return '减少'
        }
      },
      formatCostType(val) {
        switch (val) {
          case 1:
            return '商品购买'
          case 2:
            return '会员卡返利'
          case 0 :
            return "系统"
          case 3:
            return "取消订单"
        }
      }
    }
  }
</script>
