<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-av__shop}">
      <el-form :inline="true" :model="dataForm">
        <el-form-item>
          <el-input v-model="dataForm.id" placeholder="id" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-button @click="getDataList2()">{{ $t('query') }}</el-button>
        </el-form-item>
<!--        <el-form-item>-->
<!--          <el-button type="info" @click="exportHandle()">{{ $t('export') }}</el-button>-->
<!--        </el-form-item>-->
<!--        <el-form-item>-->
<!--          <el-button v-if="$hasPermission('av:shop:save')" type="primary" @click="addOrUpdateHandle()">{{ $t('add') }}</el-button>-->
<!--        </el-form-item>-->
<!--        <el-form-item>-->
<!--          <el-button v-if="$hasPermission('av:shop:delete')" type="danger" @click="deleteHandle()">{{ $t('deleteBatch') }}</el-button>-->
<!--        </el-form-item>-->
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
        <el-table-column prop="id" label="ID" header-align="center" align="center"></el-table-column>
        <el-table-column prop="name" label="商户名称" header-align="center" align="center"></el-table-column>
        <el-table-column prop="status" label="状态" header-align="center" align="center"></el-table-column>
        <el-table-column prop="profitTotal" label="总收益" header-align="center" align="center"></el-table-column>
<!--        <el-table-column prop="profitWeek" label="周收益" header-align="center" align="center"></el-table-column>-->
<!--        <el-table-column prop="profitDay" label="日收益" header-align="center" align="center"></el-table-column>-->
        <el-table-column prop="balance" label="可用金额" header-align="center" align="center"></el-table-column>
<!--        <el-table-column prop="salarys" label="总工资金额" header-align="center" align="center"></el-table-column>-->
<!--        <el-table-column prop="orders" label="累计订单数量(自己的)" header-align="center" align="center"></el-table-column>-->
<!--        <el-table-column prop="orderMoneys" label="累计订单金额(自己的)" header-align="center" align="center"></el-table-column>-->
<!--        <el-table-column prop="remark" label="备注" header-align="center" align="center"></el-table-column>-->
        <el-table-column prop="createTime" label="创建时间" header-align="center" align="center"></el-table-column>
        <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
          <template slot-scope="scope">
            <el-button v-if="$hasPermission('av:shop:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">{{ $t('update') }}</el-button>
            <el-button v-if="$hasPermission('av:shop:delete')" type="text" size="small" @click="deleteHandle(scope.row.id)">{{ $t('delete') }}</el-button>
          </template>
        </el-table-column>
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
import AddOrUpdate from './shop-add-or-update'
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/av/shop/page',
        getDataListIsPage: true,
        exportURL: '/av/shop/export',
        deleteURL: '/av/shop',
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
    getDataList2() {
      this.page = 1
      this.getDataList()
    }
  }
}
</script>
