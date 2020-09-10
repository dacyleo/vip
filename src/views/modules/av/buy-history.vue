<template>
  <el-dialog :visible.sync="visible" title="购买记录" width="70%" :close-on-click-modal="true" :close-on-press-escape="true">
    <el-card shadow="never" class="aui-card--fill">
      <div class="mod-av__user}">
<!--        <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">-->
<!--          <el-form-item>-->
<!--            <el-input v-model="dataForm.kw" placeholder="按昵称和手机号查询" clearable></el-input>-->
<!--          </el-form-item>-->
<!--          <el-form-item>-->
<!--            <el-button @click="getDataList()">{{ $t('query') }}</el-button>-->
<!--          </el-form-item>-->
<!--        </el-form>-->
        <el-table v-loading="dataListLoading" :data="dataList" border style="width: 100%;">
<!--          <el-table-column prop="id" label="ID" header-align="center" align="center"></el-table-column>-->
            <el-table-column prop="orderNo" label="订单号" header-align="center" align="center" width="180"></el-table-column>
          <el-table-column prop="nickname" label="昵称" header-align="center" align="center"></el-table-column>
          <el-table-column label="头像" header-align="center" align="center">
              <template slot-scope="scope">
                <img :src="scope.row.headImgUrl" width="40" height="40" class="head_pic"/>
              </template>
          </el-table-column>
          <el-table-column prop="money" label="交易金额" :formatter="cashConversion" header-align="center" align="center"></el-table-column>
          <el-table-column prop="createTime" label="交易时间" header-align="center" align="center"></el-table-column>
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
      </div>
    </el-card>
  </el-dialog>
</template>

<script>
import mixinViewModule from '@/mixins/view-module'
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      refId: '',
      visible: false,
      mixinViewModuleOptions: {
        getDataListURL: '/av/course/buyHistory',
        getDataListIsPage: true
      },
      dataForm: {
        kw: '',
        refId: ''
      }
    }
  },
  methods: {
    init () {
      this.visible = true
      this.dataForm.refId = this.refId
      this.getDataList()
    },
    cashConversion(row,cloumn) {
        if(row.money == null) {
            return 0.00
        }
      return row.money.toFixed(2);
    }
  }
}
</script>
