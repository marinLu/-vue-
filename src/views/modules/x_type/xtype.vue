<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.title" placeholder="标题" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="isAuth('x_type:xtype:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
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
        label="分类id">
      </el-table-column>
      <el-table-column
        prop="title"
        header-align="center"
        align="center"
        label="标题名称">
      </el-table-column>
      <el-table-column
        prop="icon"
        header-align="center"
        align="center"
        label="图标">
        <template slot-scope="scope">
          <el-popover placement="left" title="" trigger="hover">
            <img :src="scope.row.icon" style="max-height: 200px;max-width: 200px"/>
            <img slot="reference" :src="scope.row.icon"
                 :alt="scope.row.icon" style="max-height: 50px;max-width: 50px">
          </el-popover>
        </template>
      </el-table-column>
      <el-table-column
        prop="memberIcon"
        header-align="center"
        align="center"
        label="会员权益icon">
        <template slot-scope="scope">
          <el-popover placement="left" title="" trigger="hover">
            <img :src="scope.row.memberIcon" style="max-height: 200px;max-width: 200px"/>
            <img slot="reference" :src="scope.row.memberIcon"
                 :alt="scope.row.memberIcon" style="max-height: 50px;max-width: 50px">
          </el-popover>
        </template>
      </el-table-column>
      <el-table-column
        prop="vipIcon"
        header-align="center"
        align="center"
        label="会员权益icon2">
        <template slot-scope="scope">
          <el-popover placement="left" title="" trigger="hover">
            <img :src="scope.row.vipIcon" style="max-height: 200px;max-width: 200px"/>
            <img slot="reference" :src="scope.row.vipIcon"
                 :alt="scope.row.vipIcon" style="max-height: 50px;max-width: 50px">
          </el-popover>
        </template>
      </el-table-column>
      <el-table-column
        prop="pictureLink"
        header-align="center"
        align="center"
        label="绑定图片">
        <template slot-scope="scope">
          <el-popover placement="left" title="" trigger="hover">
            <img :src="scope.row.pictureLink" style="max-height: 200px;max-width: 200px"/>
            <img slot="reference" :src="scope.row.pictureLink"
                 :alt="scope.row.pictureLink" style="max-height: 50px;max-width: 50px">
          </el-popover>
        </template>
      </el-table-column>

      <el-table-column
        prop="subTitle"
        header-align="center"
        align="center"
        label="副标题">
      </el-table-column>
      <el-table-column
        prop="thriftMoney"
        header-align="center"
        align="center"
        label="节省金额">
      </el-table-column>
      <el-table-column
        prop="upperDetail"
        header-align="center"
        align="center"
        label="角标描述">
      </el-table-column>
      <el-table-column
        prop="operatorTime"
        header-align="center"
        align="center"
        label="操作时间">
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
  import AddOrUpdate from './xtype-add-or-update'
  export default {
    data () {
      return {
          url:'',
        dataForm: {
          title: ''
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
          url: this.$http.adornUrl('/x_type/xtype/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'title': this.dataForm.title
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
            url: this.$http.adornUrl('/x_type/xtype/delete'),
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
      handleTypeStatus (row) {
        this.$confirm('确认' + this.formatStatus2(row.status) + '该分类吗?', '提示', {
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
            url: this.$http.adornUrl(`/x_type/xtype/${!row.id ? 'save' : 'update'}`),
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
