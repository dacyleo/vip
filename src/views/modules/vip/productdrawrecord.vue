<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-vip__productdrawrecord}">
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
          <el-button v-if="$hasPermission('vip:productdrawrecord:save')" type="primary" @click="addOrUpdateHandle()">{{ $t('add') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('vip:productdrawrecord:delete')" type="danger" @click="deleteHandle()">{{ $t('deleteBatch') }}</el-button>
        </el-form-item>
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
        <el-table-column prop="id" label="ID" header-align="center" align="center"></el-table-column>
        <el-table-column prop="productId" label="商品ID" header-align="center" align="center"></el-table-column>
        <el-table-column prop="uid" label="领取人ID" header-align="center" align="center"></el-table-column>
        <el-table-column prop="openId" label="领取人openid,生成二维码使用，避免uid被劫持(uid目前是连续的)" header-align="center" align="center"></el-table-column>
        <el-table-column prop="state" label="0未核销1已核销" header-align="center" align="center"></el-table-column>
        <el-table-column prop="createTime" label="领取时间" header-align="center" align="center"></el-table-column>
        <el-table-column prop="updateTime" label="核销时间" header-align="center" align="center"></el-table-column>
        <el-table-column prop="money" label="核销金额" header-align="center" align="center"></el-table-column>
        <el-table-column prop="adminUid" label="核销人ID" header-align="center" align="center"></el-table-column>
        <el-table-column prop="title" label="领用优惠券标题" header-align="center" align="center"></el-table-column>
        <el-table-column prop="qrcodeContent" label="领用二维码内容" header-align="center" align="center"></el-table-column>
        <el-table-column prop="imageUrl" label="二维码地址" header-align="center" align="center"></el-table-column>
        <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
          <template slot-scope="scope">
            <el-button v-if="$hasPermission('vip:productdrawrecord:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">{{ $t('update') }}</el-button>
            <el-button v-if="$hasPermission('vip:productdrawrecord:delete')" type="text" size="small" @click="deleteHandle(scope.row.id)">{{ $t('delete') }}</el-button>
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
  }
}
</script>
