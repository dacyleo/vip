<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-vip__user}">
      <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
        <el-form-item>
          <el-input v-model="dataForm.nickname" placeholder="昵称" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-button @click="getDataList()">{{ $t('query') }}</el-button>
        </el-form-item>
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border style="width: 100%;">
        <el-table-column prop="nickname" label="昵称" header-align="center" align="center"></el-table-column>
        <el-table-column prop="headImgUrl" label="头像" header-align="center" align="center">
          <template slot-scope="scope">
            <img :src="scope.row.headImgUrl" width="100" height="60" class="head_pic"/>
          </template>
        </el-table-column>
        <el-table-column prop="phone" label="手机号码" header-align="center" align="center"></el-table-column>
        <el-table-column prop="province" label="省" header-align="center" align="center"></el-table-column>
        <el-table-column prop="city" label="市" header-align="center" align="center"></el-table-column>
<!--        <el-table-column prop="createTime" label="加入时间" header-align="center" align="center"></el-table-column>-->
        <el-table-column prop="vipTime" label="成为VIP时间" header-align="center" align="center"></el-table-column>
        <el-table-column prop="vipValidateTime" label="VIP到期时间" header-align="center" align="center"></el-table-column>

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
import AddOrUpdate from './user-add-or-update'
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/vip/user/page',
        getDataListIsPage: true,
        exportURL: '/vip/user/export',
        deleteURL: '/vip/user',
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
