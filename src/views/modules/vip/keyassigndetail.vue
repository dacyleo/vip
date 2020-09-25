<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-vip__keyassigndetail}">
      <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
        <el-form-item>
          <el-input v-model="dataForm.userName" placeholder="使用者" clearable></el-input>
        </el-form-item>
          <el-form-item>
              使用情况:
              <el-select v-model="dataForm.state" placeholder="使用情况" clearable >
                  <el-option label="全部" value=""></el-option>
                  <el-option label="未使用" value="0"></el-option>
                  <el-option label="已使用" value="1"></el-option>
              </el-select>
          </el-form-item>
        <el-form-item>
          <el-button @click="getDataList()">{{ $t('query') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('vip:keyassigndetail:save')" type="warning" @click="createKey()">生成激活码</el-button>
        </el-form-item>
          <el-form-item>
              <el-button v-if="$hasPermission('vip:keyassigndetail:save')" type="warning" @click="addOrUpdateHandle()()">发送激活码</el-button>
          </el-form-item>
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border style="width: 100%;">
<!--        <el-table-column prop="assignId" label="分配ID" header-align="center" align="center"></el-table-column>-->
<!--        <el-table-column prop="sysUserId" label="系统用户ID（sys_user）" header-align="center" align="center"></el-table-column>-->
        <el-table-column prop="keyNo" label="激活码" header-align="center" align="center"></el-table-column>
        <el-table-column prop="state" label="使用情况" :formatter="formatState" header-align="center" align="center"></el-table-column>
        <el-table-column prop="userName" label="使用者" header-align="center" align="center"></el-table-column>
        <el-table-column prop="phone" label="手机号" header-align="center" align="center"></el-table-column>
        <el-table-column prop="createTime" label="生成时间" header-align="center" align="center"></el-table-column>
        <el-table-column prop="usedTime" label="使用时间" header-align="center" align="center"></el-table-column>
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
import AddOrUpdate from './keyassigndetail-add-or-update'
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/vip/keyassigndetail/page',
        getDataListIsPage: true,
        exportURL: '/vip/keyassigndetail/export',
        deleteURL: '/vip/keyassigndetail',
        keyURL: '/vip/keyassigndetail/create-key',
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
    createKey () {
      this.$confirm('确定生成激活码?', { 'handle': '生成激活码' }, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$http.get(`${this.mixinViewModuleOptions.keyURL}`).then(({ data: res }) => {
          if (res.code !== 0) {
            return this.$message.error(res.msg)
          }else{
            this.$message({
              message: '"成功生成100个激活码"',
              type: 'success',
              duration: 1000,
              onClose: () => {
                this.getDataList()
              }
            })
          }
        }).catch(() => {})
      })

    },
    formatState (row, column) {
      if (row.state == null) {
        return ''
      }
      if (row.state === 0) {
        return '未使用'
      } else if (row.state === 1) {
        return '已使用'
      }else {
        return '未知'
      }
    },
  },
}
</script>
