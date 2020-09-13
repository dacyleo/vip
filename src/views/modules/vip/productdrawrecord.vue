<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-vip__productdrawrecord}">
      <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
        <el-form-item>
          <el-input v-model="dataForm.title" placeholder="优惠券名称" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-button @click="getDataList()">{{ $t('query') }}</el-button>
        </el-form-item>
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border  style="width: 100%;">
        <el-table-column prop="title" label="优惠券名称" header-align="center" align="center"></el-table-column>
        <el-table-column prop="userName" label="领取人" header-align="center" align="center"></el-table-column>
        <el-table-column prop="createTime" label="领取时间" header-align="center" align="center"></el-table-column>
        <el-table-column prop="state" label="核销状态" :formatter="formatState" header-align="center" align="center"></el-table-column>
        <el-table-column prop="money" label="核销金额" header-align="center" align="center"></el-table-column>
        <el-table-column prop="adminUid" label="核销人ID" header-align="center" align="center"></el-table-column>
        <el-table-column prop="updateTime" label="核销时间" header-align="center" align="center"></el-table-column>
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
import AddOrUpdate from './productdrawrecord-add-or-update'
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/vip/productdrawrecord/page',
        getDataListIsPage: true,
        exportURL: '/vip/productdrawrecord/export',
        deleteURL: '/vip/productdrawrecord',
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
    //0未核销1已核销
    formatState: function (row) {
      if (row.state == null) {
        return ''
      }
      if (row.state === 0) {
        return '未核销'
      } else if (row.state === 1) {
        return '已核销'
      } else {
        return '未知'
      }
    }
  }
}
</script>
