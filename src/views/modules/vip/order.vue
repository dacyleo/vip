<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-vip__order}">
      <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
        <el-form-item>
          <el-input v-model="dataForm.userName" placeholder="用户名称" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-button @click="getDataList()">{{ $t('query') }}</el-button>
        </el-form-item>
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border  style="width: 100%;">
        <el-table-column prop="userName" label="用户名称" header-align="center" align="center"></el-table-column>
        <el-table-column prop="orderNo" label="订单编号" header-align="center" align="center"></el-table-column>
        <el-table-column prop="status" label="订单状态" :formatter="formatStatus" header-align="center" align="center"></el-table-column>
        <el-table-column prop="payMoney" label="支付金额" header-align="center" align="center"></el-table-column>
        <el-table-column prop="payStatus" label="支付状态" :formatter="formatPayStatus" header-align="center" align="center"></el-table-column>
        <el-table-column prop="payTime" label="支付时间" header-align="center" align="center"></el-table-column>
        <el-table-column prop="createTime" label="创建时间" header-align="center" align="center"></el-table-column>
      </el-table>
      <el-pagination
        :current-page="page"
        :page-sizes="[10, 20, 50, 100]"
        :page-size="limit"
        :total="total"
        layout="total, sizes, prev, pager, next, jumper"
        @size-change="pageSizeChangeHandle"
        @current-change="pageCurrentChangeHandle">
      </el-pagination>
      <!-- 弹窗, 新增 / 修改 -->
      <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
    </div>
  </el-card>
</template>

<script>
import mixinViewModule from '@/mixins/view-module'
import AddOrUpdate from './order-add-or-update'
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/vip/order/page',
        getDataListIsPage: true,
        exportURL: '/vip/order/export',
        deleteURL: '/vip/order',
        deleteIsBatch: true
      },
      dataForm: {
        id: ''
      }
    }
  },
  components: {
    AddOrUpdate
  },
  methods: {
    //订单状态: 1待付款, 2已完成
    formatStatus: function (row) {
      if (row.status == null) {
        return ''
      }
      if (row.status === 1) {
        return '待付款'
      } else if (row.status === 2) {
        return '已完成'
      }else {
        return '未知'
      }
    },
    //1未支付, 2已支付
    formatPayStatus: function (row) {
      if (row.payStatus == null) {
        return ''
      }
      if (row.payStatus === 1) {
        return '未支付'
      } else if (row.payStatus === 2) {
        return '已支付'
      }else {
        return '未知'
      }
    }

  }
}
</script>
