<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-vip__userhistory}">
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
        <el-table-column prop="refNo" label="关联数据" header-align="center" align="center"></el-table-column>
        <el-table-column prop="type" label="升级类型" :formatter="formatType" header-align="center" align="center"></el-table-column>
<!--        <el-table-column prop="remark" label="VIP备注" header-align="center" align="center"></el-table-column>-->
        <el-table-column prop="createTime" label="升级时间" header-align="center" align="center"></el-table-column>
        <el-table-column prop="vipValidateTime" label="VIP过期时间" header-align="center" align="center"></el-table-column>
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
import AddOrUpdate from './userhistory-add-or-update'
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/vip/userhistory/page',
        getDataListIsPage: true,
        exportURL: '/vip/userhistory/export',
        deleteURL: '/vip/userhistory',
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
    formatType: function (row) {
      //类型为1是代表订单编号，类型为2代表VIP兑换码
      if (row.type == null) {
        return ''
      }
      if (row.type === 1) {
        return '缴费升级'
      } else if (row.type === 2) {
        return '激活码升级'
      }else {
        return '未知'
      }
    }
  }
}
</script>
