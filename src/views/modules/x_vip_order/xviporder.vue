<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.nickName" placeholder="昵称" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.mobile" placeholder="手机号" clearable></el-input>
      </el-form-item>
      <el-form-item label="类型">
        <el-select v-model="dataForm.type" placeholder="请选择">
          <el-option label="全部" value=""></el-option>
          <el-option label="有效会员" value="1"></el-option>
          <el-option label="过期会员" value="2"></el-option>
        </el-select>
      </el-form-item>
<!--      <el-form-item label="支付状态">-->
<!--        <el-select v-model="dataForm.status" placeholder="请选择">-->
<!--          <el-option label="全部" value="-2"></el-option>-->
<!--          <el-option label="已支付" value="1"></el-option>-->
<!--          <el-option label="未支付" value="0"></el-option>-->
<!--        </el-select>-->
<!--      </el-form-item>-->

      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
      </el-form-item>
      <el-form-item>
       <span style="color: red">会员数量: {{this.count}}</span>
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
        label="会员订单id">
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
        prop="mobile"
        header-align="center"
        align="center"
        label="手机号">
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
        prop="createDate"
        header-align="center"
        align="center"
        label="购买时间">
      </el-table-column>
      <el-table-column
        prop="validTime"
        header-align="center"
        align="center"
        label="有效期,单位月">
        <template slot-scope="scope">
          <span v-if="scope.row.isSingle!==-1">次卡</span>
          <span v-else>{{scope.row.validTime}}</span>
        </template>
      </el-table-column>
      <el-table-column
        prop="isSingle"
        header-align="center"
        align="center"
        label="使用限制">
        <template slot-scope="scope">
          <span v-if="scope.row.isSingle===1">单次使用</span>
          <span v-else>时限内使用</span>
        </template>
      </el-table-column>
      <el-table-column
        prop="isUse"
        header-align="center"
        align="center"
        label="次卡是否被使用">
        <template slot-scope="scope">
          <span v-if="scope.row.isUse===1">已使用</span>
          <span v-else-if="scope.row.isSingle===1">未使用</span>
          <span v-else>非次卡</span>
        </template>
      </el-table-column>
      <el-table-column
        prop="expirationDate"
        header-align="center"
        align="center"
        label="截止日期">
      </el-table-column>
      <el-table-column
        prop="payPrice"
        header-align="center"
        align="center"
        label="付款金额">
      </el-table-column>
      <el-table-column
        prop="discount"
        header-align="center"
        align="center"
        label="折扣">
      </el-table-column>
<!--      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">修改</el-button>
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
  </div>
</template>

<script>
  import AddOrUpdate from './xviporder-add-or-update'
  export default {
    data () {
      return {
        dataForm: {
          mobile: '',
          type: '',
          status: '-2',
          nickName: ''
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false,
        count:0
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
          url: this.$http.adornUrl('/x_vip_order/xviporder/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'mobile': this.dataForm.mobile,
            'type': this.dataForm.type,
            'status': this.dataForm.status,
            'nickName': this.dataForm.nickName
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.dataList = data.page.list
            this.totalPage = data.page.totalCount
            this.count = data.count
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
      }
    }
  }
</script>
