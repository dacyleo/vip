<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-vip__shopproduct}">
      <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
        <el-form-item>
          <el-input v-model="dataForm.id" placeholder="id" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-button @click="getDataList()">{{ $t('query') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button type="info" @click="exportHandle()">{{ $t('export') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('vip:shopproduct:save')" type="primary" @click="addOrUpdateHandle()">{{ $t('add') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('vip:shopproduct:delete')" type="danger" @click="deleteHandle()">{{ $t('deleteBatch') }}</el-button>
        </el-form-item>
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
        <el-table-column prop="id" label="ID" header-align="center" align="center"></el-table-column>
        <el-table-column prop="price" label="优惠券金额" header-align="center" align="center"></el-table-column>
        <el-table-column prop="shopId" label="商户ID" header-align="center" align="center"></el-table-column>
        <el-table-column prop="title" label="优惠券简介" header-align="center" align="center"></el-table-column>
        <el-table-column prop="content" label="优惠券说明" header-align="center" align="center"></el-table-column>
        <el-table-column prop="drawAmount" label="领取个数" header-align="center" align="center"></el-table-column>
        <el-table-column prop="verifyAmount" label="核销个数" header-align="center" align="center"></el-table-column>
        <el-table-column prop="createTime" label="" header-align="center" align="center"></el-table-column>
        <el-table-column prop="state" label="0草稿1上架2下架" header-align="center" align="center"></el-table-column>
        <el-table-column prop="picUrl" label="商品图片" header-align="center" align="center"></el-table-column>
        <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
          <template slot-scope="scope">
            <el-button v-if="$hasPermission('vip:shopproduct:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">{{ $t('update') }}</el-button>
            <el-button v-if="$hasPermission('vip:shopproduct:delete')" type="text" size="small" @click="deleteHandle(scope.row.id)">{{ $t('delete') }}</el-button>
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
import AddOrUpdate from './shopproduct-add-or-update'
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/vip/shopproduct/page',
        getDataListIsPage: true,
        exportURL: '/vip/shopproduct/export',
        deleteURL: '/vip/shopproduct',
        deleteIsBatch: true
      },
      dataForm: {
        id: ''
      }
    }
  },
  components: {
    AddOrUpdate
  }
}
</script>
