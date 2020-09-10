<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-av__trade}">
      <el-form :inline="true" :model="dataForm">
        <el-form-item>
          <el-input v-model="dataForm.kw" placeholder="按昵称查询" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-button @click="getDataList2()">{{ $t('query') }}</el-button>
        </el-form-item>
<!--        <el-form-item>-->
<!--          <el-button type="info" @click="exportHandle()">{{ $t('export') }}</el-button>-->
<!--        </el-form-item>-->
<!--        <el-form-item>-->
<!--          <el-button v-if="$hasPermission('av:trade:save')" type="primary" @click="addOrUpdateHandle()">{{ $t('add') }}</el-button>-->
<!--        </el-form-item>-->
<!--        <el-form-item>-->
<!--          <el-button v-if="$hasPermission('av:trade:delete')" type="danger" @click="deleteHandle()">{{ $t('deleteBatch') }}</el-button>-->
<!--        </el-form-item>-->
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
<!--        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>-->
        <el-table-column prop="id" label="ID" header-align="center" align="center"></el-table-column>
        <el-table-column prop="nickname" label="用户昵称" header-align="center" align="center" ></el-table-column>
        <el-table-column prop="type" label="交易类型" :formatter="dealType" header-align="center" align="center"></el-table-column>
<!--        <el-table-column prop="refId" label="关联ID" header-align="center" align="center"></el-table-column>-->
<!--        <el-table-column prop="orderNo" label="内部交易编号" header-align="center" align="center"></el-table-column>-->
<!--        <el-table-column prop="tradeNo" label="外部交易编号(支付宝相关)" header-align="center" align="center"></el-table-column>-->
        <el-table-column prop="beforeBalance" label="交易前余额" :formatter="beforeTheDeal" header-align="center" align="center" ></el-table-column>
        <el-table-column prop="money" label="交易金额" :formatter="transactionAmount" header-align="center" align="center"></el-table-column>
        <el-table-column prop="balance" label="交易后余额" :formatter="afterTheTransaction" header-align="center" align="center"></el-table-column>
        <el-table-column prop="remark" label="备注" header-align="center" align="center" width="260"></el-table-column>
        <el-table-column prop="createTime" label="创建时间" header-align="center" align="center"></el-table-column>
<!--        <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">-->
<!--          <template slot-scope="scope">-->
<!--            <el-button v-if="$hasPermission('av:trade:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">{{ $t('update') }}</el-button>-->
<!--            <el-button v-if="$hasPermission('av:trade:delete')" type="text" size="small" @click="deleteHandle(scope.row.id)">{{ $t('delete') }}</el-button>-->
<!--          </template>-->
<!--        </el-table-column>-->
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
import AddOrUpdate from './trade-add-or-update'
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/av/trade/page',
        getDataListIsPage: true,
        exportURL: '/av/trade/export',
        deleteURL: '/av/trade',
        deleteIsBatch: true
      },
      dataForm: {
        kw: ''
      }
    }
  },
  components: {
    AddOrUpdate
  },
  methods: {
      getDataList2() {
          this.page = 1
          this.getDataList()
      },
    //交易类型: 1课程购买, 2合伙人收益, 3推广收益, 4店铺收益, 5提现, 6退款, 7工资发放
    dealType(row,column) {
      if(row.type === null){
        return ''
      }
      if(row.type === 1){
        return '课程购买'
      }
      if(row.type === 2){
        return '合伙人收益'
      }
      if(row.type === 3){
        return '推广收益'
      }
      if(row.type === 4){
        return '店铺收益'
      }
      if(row.type === 5){
        return '提现'
      }
      if(row.type === 6){
        return '退款'
      }
      if(row.type === 7){
        return '工资发放'
      }
      else{
        return '未知'
      }
    },
    beforeTheDeal (row,column) {
      return row.beforeBalance.toFixed(2)
    },
    transactionAmount (row,column) {
      return row.money.toFixed(2)
    },
    afterTheTransaction (row,column) {
      return row.balance.toFixed(2)
    }

  }
}
</script>
