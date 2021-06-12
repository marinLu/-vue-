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
        <el-button v-if="isAuth('x_shop:xshop:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
        <el-button v-if="isAuth('x_shop:xshop:update')" type="primary" @click="updatebusinessTime()">设置所有店铺营业时间</el-button>
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
        prop="createTime"
        header-align="center"
        align="center"
        label="店铺申请时间">
      </el-table-column>
      <el-table-column
        prop="everyDayNum"
        header-align="center"
        align="center"
        label="当日订单">
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
          <el-button type="text" size="small" @click="handleReceiveStatus(scope.row,1)" v-if="scope.row.receiveStatus==0">审核通过</el-button>
          <el-button type="text" size="small" @click="handleReceiveStatus(scope.row,-1)" v-if="scope.row.receiveStatus==0">不通过</el-button>
          <el-button type="text" size="small" @click="handleReceiveStatus(scope.row,1)" v-if="scope.row.receiveStatus==-1">审核通过</el-button>
          <el-button type="text" size="small" @click="handleReceiveStatus(scope.row,-1)" v-if="scope.row.receiveStatus==1">不通过</el-button>
          <el-button type="text" size="small" @click="addOrUpdateService(0,scope.row.id)">新增服务</el-button>
          <el-button type="text" size="small" @click="walletDetail(scope.row.id)">服务列表</el-button>
          <el-button type="text" size="small" @click="orderDetail(scope.row.id)">服务订单</el-button>
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
    <add-or-update-Service v-if="addOrUpdateServiceVisible" ref="addOrUpdateService" @refreshDataList="getDataList"></add-or-update-Service>
    <el-dialog center title="服务订单" :visible.sync="dialogFormVisible" @close='closeDialog'>
      <template>
        <div class="mod-config">
          <el-form :inline="true" :model="orderdataForm" @keyup.enter.native="walletDetailData()">
            <el-form-item>
              <el-input v-model="orderdataForm.orderNum" placeholder="订单号" clearable></el-input>
            </el-form-item>
            <el-form-item label="状态">
              <el-select v-model="orderdataForm.status" placeholder="请选择">
                <el-option label="全部" value=""></el-option>
                <el-option label="已支付" value="1"></el-option>
                <el-option label="未支付" value="0"></el-option>
              </el-select>
            </el-form-item>
            <el-form-item>
              <el-button @click="orderDetailData()">查询</el-button>
            </el-form-item>
          </el-form>
          <el-table
            :data="orderdataList"
            border
            v-loading="orderdataListLoading"
            style="width: 100%;">
            <el-table-column
              prop="id"
              header-align="center"
              align="center"
              label="服务id">
            </el-table-column>
            <el-table-column
              prop="orderNum"
              header-align="center"
              align="center"
              label="服务订单号">
            </el-table-column>
            <el-table-column
              prop="shopId"
              header-align="center"
              align="center"
              label="商店id">
            </el-table-column>
            <el-table-column
              prop="userId"
              header-align="center"
              align="center"
              label="用户id">
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
              prop="payPrice"
              header-align="center"
              align="center"
              label="支付金额">
            </el-table-column>
            <el-table-column
              prop="vipFreePrice"
              header-align="center"
              align="center"
              label="会员免除金额">
            </el-table-column>
            <el-table-column
              prop="orderPic"
              header-align="center"
              align="center"
              label="上传拍照订单链接">
              <template slot-scope="scope">
                <el-popover placement="left" title="" trigger="hover">
                  <img :src="scope.row.orderPic" style="max-height: 200px;max-width: 200px"/>
                  <img slot="reference" :src="scope.row.orderPic"
                       :alt="scope.row.orderPic" style="max-height: 50px;max-width: 50px">
                </el-popover>
              </template>
            </el-table-column>
            <el-table-column
              prop="status"
              header-align="center"
              align="center"
              label="支付状态">
              <template slot-scope="scope">
                {{formatServiceOrderStatus(scope.row.status)}}
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
            :current-page="orderdataForm.pageIndex2"
            :page-sizes="[10, 20, 50, 100]"
            :page-size="orderdataForm.pageSize2"
            :total="orderdataForm.totalPage2"
            layout="total, sizes, prev, pager, next, jumper">
          </el-pagination>
        </div>
      </template>
    </el-dialog>
    <el-dialog center title="服务列表" :visible.sync="dialogFormVisible2" @close='closeDialog'>
      <template>
        <div class="mod-config">
          <el-form :inline="true" :model="detaildataForm" @keyup.enter.native="walletDetailData()">
            <el-form-item>
              <el-input v-model="detaildataForm.serviceName" placeholder="服务名称" clearable></el-input>
            </el-form-item>
            <el-form-item label="状态">
              <el-select v-model="detaildataForm.status" placeholder="请选择">
                <el-option label="全部" value=""></el-option>
                <el-option label="上线" value="1"></el-option>
                <el-option label="下线" value="-1"></el-option>
              </el-select>
            </el-form-item>
            <el-form-item>
              <el-button @click="walletDetailData()">查询</el-button>
            </el-form-item>
          </el-form>
          <el-table
            :data="detaildataList"
            border
            v-loading="detaildataListLoading"
            style="width: 100%;">
            <el-table-column
              prop="id"
              header-align="center"
              align="center"
              label="服务id">
            </el-table-column>
            <el-table-column
              prop="shopId"
              header-align="center"
              align="center"
              label="商店id" v-if="show">
            </el-table-column>
            <el-table-column
              prop="serviceName"
              header-align="center"
              align="center"
              label="服务名称">
            </el-table-column>
            <el-table-column
              prop="serviceBanner"
              header-align="center"
              align="center"
              label="列表图">
              <template slot-scope="scope">
                <el-popover placement="left" title="" trigger="hover">
                  <img :src="scope.row.serviceBanner" style="max-height: 200px;max-width: 200px"/>
                  <img slot="reference" :src="scope.row.serviceBanner"
                       :alt="scope.row.serviceBanner" style="max-height: 50px;max-width: 50px">
                </el-popover>
              </template>
            </el-table-column>
            <el-table-column
              prop="serviceDetail"
              header-align="center"
              align="center"
              label="详情图">
              <template slot-scope="scope">
                <el-popover placement="left" title="" trigger="hover">
                  <img :src="scope.row.serviceDetail" style="max-height: 200px;max-width: 200px"/>
                  <img slot="reference" :src="scope.row.serviceDetail"
                       :alt="scope.row.serviceDetail" style="max-height: 50px;max-width: 50px">
                </el-popover>
              </template>
            </el-table-column>
            <el-table-column
              prop="originalPrice"
              header-align="center"
              align="center"
              label="原价">
            </el-table-column>
            <el-table-column
              prop="isDiscount"
              header-align="center"
              align="center"
              label="是否折扣">
              <template slot-scope="scope">
                {{formatIsDiscount(scope.row.isDiscount)}}
              </template>
            </el-table-column>
            <el-table-column
              prop="discountPrice"
              header-align="center"
              align="center"
              label="折扣金额">
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
                <el-button type="text" size="small" @click="addOrUpdateService(scope.row.id,0)">修改</el-button>
                <el-button type="text" size="small" @click="handleSrviceStatus(scope.row)" v-if="scope.row.status==1">下线</el-button>
                <el-button type="text" size="small" @click="handleSrviceStatus(scope.row)" v-else-if="scope.row.status==-1">上线</el-button>
              </template>
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
    <el-dialog center title="一键设置所有店铺营业时间" :visible.sync="dialogFormVisible3" @close='closeDialog'>
      <el-form label-width="150px">
        <el-form-item label="营业时间" prop="businessTime">
          <el-time-picker
            is-range
            v-model="businessTime"
            format="HH:mm"
            value-format="HH:mm"
            range-separator="至"
            start-placeholder="开始时间"
            end-placeholder="结束时间"
            placeholder="选择时间范围">
          </el-time-picker>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
      <el-button @click='closeDialog()'>取消</el-button>
      <el-button type="primary" @click="updateBussTime()">确定</el-button>
    </span>
    </el-dialog>
  </div>
</template>

<script>
  import AddOrUpdate from './xshop-add-or-update'
  import AddOrUpdateService from '../x_shop_service/xshopservice-add-or-update'
  export default {
    data () {
      return {
        show:false,
        businessTime:'',
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
          shopId:'',
          pageIndex3:1,
          pageSize3: 10,
          totalPage3: 0,
          serviceName:'',
          status:''
        },
        dialogFormVisible2:false,
        detaildataList:[],

        orderdataListLoading:false,
        orderdataForm:{
          shopId:'',
          pageIndex2:1,
          pageSize2: 10,
          totalPage2: 0,
          orderNum:'',
          status:''
        },
        dialogFormVisible:false,
        orderdataList:[],
        dialogFormVisible3:false,
      }
    },
    components: {
      AddOrUpdate,
      AddOrUpdateService
    },
    activated () {
      this.getDataList()
    },
    methods: {
      closeDialog(){
        this.dialogFormVisible = false
        this.dialogFormVisible2 = false
        this.dialogFormVisible3 = false
        this.detaildataForm.id = ''
      },
      updatebusinessTime(){
        this.dialogFormVisible3 = true
      },
      updateBussTime(){
        this.$http({
          url: this.$http.adornUrl('/x_shop/xshop/updateBussTime'),
          method: 'post',
          data: this.$http.adornData({
            'businessTime': this.businessTime[0]+"-"+this.businessTime[1],
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.$message({
              message: '操作成功',
              type: 'success',
              duration: 1500,
              onClose: () => {
                this.dialogFormVisible3 = false
                this.businessTime = ''
                this.getDataList()
              }
            })
          } else {
            this.$message.error(data.msg)
          }
        })
      },
      walletDetail(shopId){
        this.detaildataForm.shopId = shopId
        this.dialogFormVisible2 = true
        this.walletDetailData()
      },
      walletDetailData(){
        this.detaildataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/x_shop_service/xshopservice/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.detaildataForm.pageIndex3,
            'limit': this.detaildataForm.pageSize3,
            'shopId': this.detaildataForm.shopId,
            'serviceName':this.detaildataForm.serviceName,
            'status':this.detaildataForm.status
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
      orderDetailData(){
        this.detaildataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/x_service_order/xserviceorder/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.orderdataForm.pageIndex2,
            'limit': this.orderdataForm.pageSize2,
            'shopId': this.orderdataForm.shopId,
            'orderNum':this.orderdataForm.orderNum,
            'status':this.orderdataForm.status
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.orderdataList = data.page.list
            this.orderdataForm.totalPage2 = data.page.totalCount
          } else {
            this.orderdataList = []
            this.orderdataForm.totalPage2 = 0
          }
          this.orderdataListLoading = false
        })
      },
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/x_shop/xshop/list'),
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
      sizeChangeHandle2 (val) {
        this.orderdataForm.pageSize2 = val
        this.orderdataForm.pageIndex2 = 1
        this.orderDetailData()
      },
      // 当前页
      currentChangeHandle2 (val) {
        this.orderdataForm.pageIndex2 = val
        this.orderDetailData()
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
      // 新增 / 修改
      addOrUpdateHandle (id) {
        this.addOrUpdateVisible = true
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init(id)
        })
      },
      addOrUpdateService (id,shopId) {
        this.addOrUpdateServiceVisible = true
        this.$nextTick(() => {
          this.$refs.addOrUpdateService.init(id,shopId)
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
            url: this.$http.adornUrl('/x_shop/xshop/delete'),
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
        this.$confirm('确认' + this.formatStatus2(row.status) + '该商店吗?', '提示', {
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
            url: this.$http.adornUrl(`/x_shop/xshop/${!row.id ? 'save' : 'updateStatus'}`),
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
      handleTypeStatus(row){
        this.$confirm('确认' + this.formatStatus2(row.status) + '该商店吗?', '提示', {
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
            url: this.$http.adornUrl(`/x_shop/xshop/${!row.id ? 'save' : 'updateStatus'}`),
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
      handleReceiveStatus(row,status){
        this.$confirm('确认' + this.formatReceiveStatus(status) + '该商店吗?', '提示', {
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl(`/x_shop/xshop/${!row.id ? 'save' : 'updateReStatus'}`),
            method: 'post',
            data: this.$http.adornData({
              'id': row.id,
              'receiveStatus':status
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
      handleSrviceStatus(row){
        this.$confirm('确认' + this.formatStatus2(row.status) + '该服务吗?', '提示', {
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
            url: this.$http.adornUrl(`/x_shop_service/xshopservice/${!row.id ? 'save' : 'update'}`),
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
                  this.walletDetailData()
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
      }
    }
  }
</script>
