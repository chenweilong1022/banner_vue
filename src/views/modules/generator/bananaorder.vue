<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.key" placeholder="参数名" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-select v-model="dataForm.topUpWay" placeholder="请选择">
          <el-option
            v-for="item in options"
            :key="item.value"
            :label="item.label"
            :value="item.value">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="isAuth('generator:bananaorder:delete')" type="danger" @click="deleteHandle()" :disabled="dataListSelections.length <= 0">批量删除</el-button>
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column
        type="selection"
        header-align="center"
        align="center"
        width="50">
      </el-table-column>
      <el-table-column
        prop="orderid"
        header-align="center"
        align="center"
        label="订单id">
      </el-table-column>
      <el-table-column
        prop="title"
        header-align="center"
        align="center"
        label="商品名称">
      </el-table-column>
      <el-table-column
        prop="count"
        header-align="center"
        align="center"
        label="购买数量">
      </el-table-column>
      <el-table-column
        prop="price"
        header-align="center"
        align="center"
        label="购买价钱">
      </el-table-column>
      <el-table-column
        prop="totalPrice"
        header-align="center"
        align="center"
        label="商品总价格">
      </el-table-column>
      <el-table-column
        prop="paytime"
        header-align="center"
        align="center"
        label="支付时间">
      </el-table-column>
      <el-table-column
        prop="payType"
        header-align="center"
        :formatter="formatterPayType"
        align="center"
        label="支付方式">
      </el-table-column>
      <el-table-column
        prop="account"
        header-align="center"
        align="center"
        label="充值账号">
      </el-table-column>
      <el-table-column
        prop="topUpWay"
        header-align="center"
        :formatter="formatterColumn"
        align="center"
        label="充值方式">
      </el-table-column>
      <el-table-column
        prop="password"
        header-align="center"
        align="center"
        label="卡密">
      </el-table-column>
      <el-table-column
        prop="bananaUserEntity.phone"
        header-align="center"
        align="center"
        label="用户">
      </el-table-column>
      <el-table-column
        prop="status"
        header-align="center"
        align="center"
        :formatter="formatterStatus"
        label="是否已经充值">
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.orderid)">详情</el-button>
          <el-button type="text" size="small" @click="deleteHandle(scope.row.orderid)">删除</el-button>
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
  import AddOrUpdate from './bananaorder-add-or-update'
  export default {
    data () {
      return {
        dataForm: {
          key: '',
          topUpWay: ''
        },
        options: [{
          value: 1,
          label: '卡密'
        }, {
          value: 2,
          label: '自动充值'
        }, {
          value: 3,
          label: '手动充值'
        }],
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
          url: this.$http.adornUrl('/generator/bananaorder/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'key': this.dataForm.key,
            'topUpWay': this.dataForm.topUpWay
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
      formatterColumn(row, column) {
        switch(row.topUpWay){
            case 1:
            return '卡密';
            break;

            case 2:
            return '自动充值';
            break;

            case 3:
            return '手动充值';
            break;
        }
      },
      formatterStatus(row, column) {
        switch(row.status){
            case 0:
            return '未充值';
            break;

            case 1:
            return '已经充值';
            break;


        }
      },
      formatterPayType(row, column) {
        switch(row.payType){
            case 1:
            return '微信';
            break;

            case 2:
            return '支付宝';
            break;
        }
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
          return item.orderid
        })
        this.$confirm(`确定对[id=${ids.join(',')}]进行[${id ? '删除' : '批量删除'}]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/generator/bananaorder/delete'),
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
      }
    }
  }
</script>
