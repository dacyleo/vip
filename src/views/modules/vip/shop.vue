<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-vip__shop}">
      <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
        <el-form-item>
          <el-input v-model="dataForm.name" placeholder="商户名称" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-button @click="getDataList()">{{ $t('query') }}</el-button>
        </el-form-item>
<!--        <el-form-item>-->
<!--          <el-button type="info" @click="exportHandle()">{{ $t('export') }}</el-button>-->
<!--        </el-form-item>-->
        <el-form-item>
          <el-button v-if="$hasPermission('vip:shop:save')" type="primary" @click="addOrUpdateHandle()">{{ $t('add') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('vip:shop:delete')" type="danger" @click="deleteHandle()">{{ $t('deleteBatch') }}</el-button>
        </el-form-item>
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
<!--        <el-table-column prop="id" label="ID" header-align="center" align="center"></el-table-column>-->
        <el-table-column prop="type" :formatter="figureType" label="商户类型" header-align="center" align="center"></el-table-column>
        <el-table-column prop="name" label="商户名称" header-align="center" align="center"></el-table-column>
        <el-table-column prop="picUrl" label="商户图片" header-align="center" align="center">
          <template slot-scope="scope">
            <img :src="scope.row.picUrl" width="100" height="60" class="head_pic"/>
          </template>
        </el-table-column>
<!--        <el-table-column prop="content" label="商户简介" header-align="center" align="center"></el-table-column>-->
<!--        <el-table-column prop="sysUserId" label="商户管理员ID" header-align="center" align="center"></el-table-column>-->
        <el-table-column prop="phone" label="核销员手机号" header-align="center" align="center"></el-table-column>
        <el-table-column prop="createTime" label="创建时间" header-align="center" align="center"></el-table-column>
        <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
          <template slot-scope="scope">
            <el-button v-if="$hasPermission('vip:shop:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">{{ $t('update') }}</el-button>
            <el-button v-if="$hasPermission('vip:shop:delete')" type="text" size="small" @click="deleteHandle(scope.row.id)">{{ $t('delete') }}</el-button>
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
        getDataListURL: '/vip/shop/page',
        getDataListIsPage: true,
        exportURL: '/vip/shop/export',
        deleteURL: '/vip/shop',
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
    figureType (row, column) {
      if (row.type == null) {
        return ''
      }
      if (row.type === 0) {
        return '普通商户'
      } else if (row.type === 1) {
        return '0元购商户'
      }else {
        return '未知'
      }
    },
  }
}
</script>
