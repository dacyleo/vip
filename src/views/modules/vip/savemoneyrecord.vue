<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-vip__savemoneyrecord}">
      <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
        <el-form-item>
          <el-input v-model="dataForm.userName" placeholder="领取人" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-button @click="getDataList()">{{ $t('query') }}</el-button>
        </el-form-item>
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border  style="width: 100%;">
        <el-table-column prop="userName" label="领取人" header-align="center" align="center"></el-table-column>
        <el-table-column prop="type" label="优惠类型" :formatter="formatType" header-align="center" align="center"></el-table-column>
        <el-table-column prop="name" label="优惠名称" header-align="center" align="center"></el-table-column>
        <el-table-column prop="money" label="价值（元）" header-align="center" align="center"></el-table-column>
        <el-table-column prop="createTime" label="领取时间" header-align="center" align="center"></el-table-column>
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
import AddOrUpdate from './savemoneyrecord-add-or-update'
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/vip/savemoneyrecord/page',
        getDataListIsPage: true,
        exportURL: '/vip/savemoneyrecord/export',
        deleteURL: '/vip/savemoneyrecord',
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
    //0外卖1视频2商品券3零元换购
    formatType: function (row) {
      if (row.type == null) {
        return ''
      }
      if (row.type === 0) {
        return '外卖会员'
      } else if (row.type === 1) {
        return '视频会员'
      } else if (row.type === 2) {
        return '优惠券'
      } else if (row.type === 3) {
        return '0元购'
      }else {
        return '未知'
      }
    }
  }
}
</script>
