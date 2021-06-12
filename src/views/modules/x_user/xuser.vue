<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.nickName" placeholder="昵称" clearable></el-input>
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
        prop="mobile"
        header-align="center"
        align="center"
        label="手机号">
      </el-table-column>
      <el-table-column
        prop="city"
        header-align="center"
        align="center"
        label="城市">
      </el-table-column>
      <el-table-column
        prop="openId"
        header-align="center"
        align="center"
        label="openId">
      </el-table-column>
      <el-table-column
        prop="unionId"
        header-align="center"
        align="center"
        label="union_id">
      </el-table-column>
      <el-table-column
        prop="mCode"
        header-align="center"
        align="center"
        label="个人二维码">
        <template slot-scope="scope">
          <el-popover placement="left" title="" trigger="hover">
            <img :src="scope.row.mCode" style="max-height: 200px;max-width: 200px"/>
            <img slot="reference" :src="scope.row.mCode"
                 :alt="scope.row.mCode" style="max-height: 50px;max-width: 50px">
          </el-popover>
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
         <!-- <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">修改</el-button>
          <el-button type="text" size="small" @click="deleteHandle(scope.row.id)">删除</el-button>-->
          <el-button type="text" size="small" @click="userCar(scope.row.id)">用户座驾</el-button>
      <!--    <el-button type="text" size="small" @click="walletDetail(scope.row.id)">钱包明细</el-button>-->
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
    <el-dialog center title="用户座驾" :visible.sync="dialogFormVisible" @close='closeDialog'>
      <template>
        <div class="mod-config">
          <el-form :model="detaildataForm" label-width="120px">
            <el-form-item label="汽车型号">
              <el-input v-model="detaildataForm.carModel" placeholder="汽车型号"></el-input>
            </el-form-item>
            <el-form-item label="车牌号">
              <el-input v-model="detaildataForm.licensePlate" placeholder="车牌号"></el-input>
            </el-form-item>
            <el-form-item label="车辆注册日期">
              <el-input v-model="detaildataForm.registerDate" placeholder="车辆注册日期"></el-input>
            </el-form-item>
            <el-form-item label="校验有效期">
              <el-input v-model="detaildataForm.validDate" placeholder="校验有效期"></el-input>
            </el-form-item>
            <el-form-item label="车辆类型">
              <el-input v-model="detaildataForm.carType" placeholder="车辆类型"></el-input>
            </el-form-item>
            <el-form-item label="车辆所属">
              <el-input v-model="detaildataForm.carBelong" placeholder="车辆所属"></el-input>
            </el-form-item>
            <el-form-item label="车辆使用性质">
              <el-input v-model="detaildataForm.carNatureUse" placeholder="车辆使用性质"></el-input>
            </el-form-item>
            <el-form-item label="车座数量">
              <el-input v-model="detaildataForm.carSeatNum" placeholder="车座数量"></el-input>
            </el-form-item>
          </el-form>
          <span slot="footer" class="dialog-footer">
            <el-button @click="closeDialog()">取消</el-button>
            <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
          </span>
        </div>
      </template>
    </el-dialog>
    <el-dialog center title="明细" :visible.sync="dialogFormVisible2" @close='closeDialog'>
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
            @size-change="sizeChangeHandle"
            @current-change="currentChangeHandle"
            :current-page="pageIndex"
            :page-sizes="[10, 20, 50, 100]"
            :page-size="pageSize"
            :total="totalPage"
            layout="total, sizes, prev, pager, next, jumper">
          </el-pagination>
        </div>
      </template>
    </el-dialog>
  </div>
</template>

<script>
  import AddOrUpdate from './xuser-add-or-update'
  function getDataListForm(){
    return {
      id:'',
      carModel:'',
      licensePlate:'',
      registerDate:'',
      validDate: '',
      carType:'',
      carBelong:'',
      carNatureUse:'',
      carSeatNum:'',
      branId:'',
      userId:'',
      logoAddress:''
    }
  }
  export default {
    data () {
      return {
        dataForm: {
          nickName: ''
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        detaildataListLoading:false,
        dataListSelections: [],
        addOrUpdateVisible: false,
        dialogFormVisible:false,
        detaildataForm:getDataListForm(),
        dialogFormVisible2:false,
        detaildataList:[]
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
        this.dialogFormVisible2 = false
        this.detaildataForm = getDataListForm()
      },
      userCar(id){
        this.detaildataForm.id = id
        this.dialogFormVisible = true
        this.getDetailDataList();
      },
      getDetailDataList () {
        this.detaildataListLoading = true
        if (this.detaildataForm.id) {
          this.$http({
            url: this.$http.adornUrl(`/x_user_car/xusercar/info/${this.detaildataForm.id}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({data}) => {
            if (data && data.code === 0) {
              console.log('---------------------><>>><>>',data.xUserCar)
              if (data.xUserCar){
                this.detaildataForm=data.xUserCar
              }
              // this.detaildataForm.carModel = data.xUserCar.carModel
              // this.detaildataForm.licensePlate = data.xUserCar.licensePlate
              // this.detaildataForm.registerDate = data.xUserCar.registerDate
              // this.detaildataForm.validDate = data.xUserCar.validDate
              // this.detaildataForm.carType = data.xUserCar.carType
              // this.detaildataForm.carBelong = data.xUserCar.carBelong
              // this.detaildataForm.carNatureUse = data.xUserCar.carNatureUse
              // this.detaildataForm.carSeatNum = data.xUserCar.carSeatNum
            }
          })
        }
      },
      dataFormSubmit(){
          this.$http({
            url: this.$http.adornUrl(`/x_user_car/xusercar/update`),
            method: 'post',
            data: this.$http.adornData({
              id:this.detaildataForm.id,
              carModel:this.detaildataForm.carModel,
              licensePlate:this.detaildataForm.licensePlate,
              registerDate:this.detaildataForm.registerDate,
              validDate: this.detaildataForm.validDate,
              carType:this.detaildataForm.carType,
              carBelong:this.detaildataForm.carBelong,
              carNatureUse:this.detaildataForm.carNatureUse,
              carSeatNum:this.detaildataForm.carSeatNum,
              branId:this.detaildataForm.branId,
              userId:this.detaildataForm.userId,
              logoAddress:this.detaildataForm.logoAddress
            })
          }).then(({data}) => {
            if (data && data.code === 0) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500,
                onClose: () => {
                  this.dialogFormVisible = false
                  this.getDataList()
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })

      },
      walletDetail(id){
        this.detaildataForm.id = id
        this.dialogFormVisible2 = true
          this.walletDetailData()
      },
      walletDetailData(){
        this.detaildataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/x_wallet_detail/xwalletdetail/list'),
          method: 'get',
          params: this.$http.adornParams({
            'userId': this.detaildataForm.id,
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
          url: this.$http.adornUrl('/x_user/xuser/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'nickName': this.dataForm.nickName
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
