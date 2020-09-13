<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-vip__luckyrecord}">
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
        <el-table-column prop="createTime" label="获得抽奖机会时间" header-align="center" align="center"></el-table-column>
<!--        <el-table-column prop="refId" label="关联奖品ID" header-align="center" align="center"></el-table-column>-->
        <el-table-column prop="state" label="抽奖情况" :formatter="formatState" header-align="center" align="center"></el-table-column>
        <el-table-column prop="refName" label="中奖结果" header-align="center" align="center"></el-table-column>

        <el-table-column prop="updateTime" label="抽奖时间" header-align="center" align="center"></el-table-column>
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
import AddOrUpdate from './luckyrecord-add-or-update'
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/vip/luckyrecord/page',
        getDataListIsPage: true,
        exportURL: '/vip/luckyrecord/export',
        deleteURL: '/vip/luckyrecord',
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
    formatState: function (row) {
      //0机会未使用，1机会已使用
      if (row.state == null) {
        return ''
      }
      if (row.state === 0) {
        return '未抽奖'
      } else if (row.state === 1) {
        return '已抽奖'
      }else {
        return '未知'
      }
    }
  }
}
</script>
