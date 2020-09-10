<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-vip__user}">
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
          <el-button v-if="$hasPermission('vip:user:save')" type="primary" @click="addOrUpdateHandle()">{{ $t('add') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('vip:user:delete')" type="danger" @click="deleteHandle()">{{ $t('deleteBatch') }}</el-button>
        </el-form-item>
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
        <el-table-column prop="id" label="ID" header-align="center" align="center"></el-table-column>
        <el-table-column prop="phone" label="手机号码" header-align="center" align="center"></el-table-column>
        <el-table-column prop="type" label="用户类型: 1普通用户, 2会员" header-align="center" align="center"></el-table-column>
        <el-table-column prop="nickname" label="昵称" header-align="center" align="center"></el-table-column>
        <el-table-column prop="remark" label="备注" header-align="center" align="center"></el-table-column>
        <el-table-column prop="openId" label="公众号openId" header-align="center" align="center"></el-table-column>
        <el-table-column prop="miniOpenId" label="小程序openId" header-align="center" align="center"></el-table-column>
        <el-table-column prop="unionId" label="微信的unionId" header-align="center" align="center"></el-table-column>
        <el-table-column prop="headImgUrl" label="头像图片地址（默认微信）" header-align="center" align="center"></el-table-column>
        <el-table-column prop="sex" label="性别: 1表示男性, 2表示女性" header-align="center" align="center"></el-table-column>
        <el-table-column prop="sexDesc" label="性别描述信息: 男、女、未知等" header-align="center" align="center"></el-table-column>
        <el-table-column prop="language" label="语言" header-align="center" align="center"></el-table-column>
        <el-table-column prop="province" label="省" header-align="center" align="center"></el-table-column>
        <el-table-column prop="city" label="市" header-align="center" align="center"></el-table-column>
        <el-table-column prop="country" label="区" header-align="center" align="center"></el-table-column>
        <el-table-column prop="createTime" label="创建时间" header-align="center" align="center"></el-table-column>
        <el-table-column prop="vipValidateTime" label="VIP到期时间" header-align="center" align="center"></el-table-column>
        <el-table-column prop="vipTime" label="成为VIP时间" header-align="center" align="center"></el-table-column>
        <el-table-column prop="signAmount" label="连续签到天数" header-align="center" align="center"></el-table-column>
        <el-table-column prop="luckyAmount" label="抽奖次数" header-align="center" align="center"></el-table-column>
        <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
          <template slot-scope="scope">
            <el-button v-if="$hasPermission('vip:user:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">{{ $t('update') }}</el-button>
            <el-button v-if="$hasPermission('vip:user:delete')" type="text" size="small" @click="deleteHandle(scope.row.id)">{{ $t('delete') }}</el-button>
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
