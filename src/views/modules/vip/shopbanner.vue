<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-vip__shopbanner}">
      <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
        <el-form-item>
          <el-input v-model="dataForm.shopName" placeholder="所属商户" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-button @click="getDataList()">{{ $t('query') }}</el-button>
        </el-form-item>
<!--        <el-form-item>-->
<!--          <el-button type="info" @click="exportHandle()">{{ $t('export') }}</el-button>-->
<!--        </el-form-item>-->
        <el-form-item>
          <el-button v-if="$hasPermission('vip:shopproduct:save')" type="warning" @click="onHandle()">上架</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('vip:shopproduct:save')" type="warning" @click="offHandle()">下架
          </el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('vip:shopbanner:save')" type="primary" @click="addOrUpdateHandle()">{{ $t('add') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('vip:shopbanner:delete')" type="danger" @click="deleteHandle()">{{ $t('deleteBatch') }}</el-button>
        </el-form-item>
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
<!--        <el-table-column prop="id" label="ID" header-align="center" align="center"></el-table-column>-->
        <el-table-column prop="shopName" label="所属商户" header-align="center" align="center"></el-table-column>
        <el-table-column prop="title" label="标题" header-align="center" align="center"></el-table-column>
        <el-table-column prop="imageUrl" label="图片" header-align="center" align="center">
          <template slot-scope="scope">
            <img :src="scope.row.imageUrl" width="100" height="60" class="head_pic"/>
          </template>
        </el-table-column>
<!--        <el-table-column prop="refUrl" label="banner链接URL" header-align="center" align="center"></el-table-column>-->
        <el-table-column prop="state" label="状态" :formatter="figureState" header-align="center" align="center"></el-table-column>
        <el-table-column prop="createTime" label="创建时间" header-align="center" align="center"></el-table-column>
        <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
          <template slot-scope="scope">
            <el-button v-if="$hasPermission('vip:shopbanner:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">{{ $t('update') }}</el-button>
            <el-button v-if="$hasPermission('vip:shopbanner:delete')" type="text" size="small" @click="deleteHandle(scope.row.id)">{{ $t('delete') }}</el-button>
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
import AddOrUpdate from './shopbanner-add-or-update'
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/vip/shopbanner/page',
        getDataListIsPage: true,
        exportURL: '/vip/shopbanner/export',
        deleteURL: '/vip/shopbanner',
        onURL: '/vip/shopbanner/on',
        offURL: '/vip/shopbanner/off',
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
    //上架
    onHandle () {
      if (!this.dataListSelections || this.dataListSelections.length == 0) {
        return this.$message({
          message: '请选择上架项',
          type: 'warning',
          duration: 1000
        })
      }
      this.$confirm('确定进行上架操作?', { 'handle': '上架' }, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$http.post(`${this.mixinViewModuleOptions.onURL}`, this.dataListSelections.map(item => item['id'])
        ).then(({ data: res }) => {
          if (res.code !== 0) {
            return this.$message.error(res.msg)
          }
          this.$message({
            message: this.$t('prompt.success'),
            type: 'success',
            duration: 1000,
            onClose: () => {
              this.getDataList()
            }
          })
        })
      })
    },
    offHandle () {
      if (!this.dataListSelections || this.dataListSelections.length === 0) {
        return this.$message({
          message: '请选择下架项',
          type: 'warning',
          duration: 1000
        })
      }
      this.$confirm('确定进行下架操作?', { 'handle': '下架' }, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$http.post(`${this.mixinViewModuleOptions.offURL}`, this.dataListSelections.map(item => item['id'])
        ).then(({ data: res }) => {
          if (res.code !== 0) {
            return this.$message.error(res.msg)
          }
          this.$message({
            message: this.$t('prompt.success'),
            type: 'success',
            duration: 1000,
            onClose: () => {
              this.getDataList()
            }
          })
        })
      })
    },
    figureState (row, column) {
      if (row.state == null) {
        return ''
      }
      if (row.state === 0) {
        return '草稿'
      } else if (row.state === 1) {
        return '已上架'
      } else if (row.state === 2) {
        return '已下架'
      } else {
        return '未知'
      }
    },
    //上架
    onHandle () {
      if (!this.dataListSelections || this.dataListSelections.length == 0) {
        return this.$message({
          message: '请选择上架项',
          type: 'warning',
          duration: 1000
        })
      }
      this.$confirm('确定进行上架操作?', { 'handle': '上架' }, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$http.post(`${this.mixinViewModuleOptions.onURL}`, this.dataListSelections.map(item => item['id'])
        ).then(({ data: res }) => {
          if (res.code !== 0) {
            return this.$message.error(res.msg)
          }
          this.$message({
            message: this.$t('prompt.success'),
            type: 'success',
            duration: 1000,
            onClose: () => {
              this.getDataList()
            }
          })
        })
      })
    },
    offHandle () {
      if (!this.dataListSelections || this.dataListSelections.length === 0) {
        return this.$message({
          message: '请选择下架项',
          type: 'warning',
          duration: 1000
        })
      }
      this.$confirm('确定进行下架操作?', { 'handle': '下架' }, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$http.post(`${this.mixinViewModuleOptions.offURL}`, this.dataListSelections.map(item => item['id'])
        ).then(({ data: res }) => {
          if (res.code !== 0) {
            return this.$message.error(res.msg)
          }
          this.$message({
            message: this.$t('prompt.success'),
            type: 'success',
            duration: 1000,
            onClose: () => {
              this.getDataList()
            }
          })
        })
      })
    },
  }
}
</script>
