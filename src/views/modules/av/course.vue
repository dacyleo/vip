<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-av__course}">
      <el-form :inline="true" :model="dataForm">
        <el-form-item>
          <el-button v-if="$hasPermission('av:course:save')" type="primary" @click="addOrUpdate()">{{ $t('add') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('av:course:save')" type="warning" @click="onHandle()">上架</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('av:course:save')" type="warning" @click="offHandle()">下架</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('av:course:delete')" type="danger" @click="deleteHandle()">{{ $t('deleteBatch') }}</el-button>
        </el-form-item>
        <el-form-item label="状态">
          <!--          状态: 1未上架, 2已上架, 3删除-->
          <el-select v-model="dataForm.status" placeholder="状态" clearable>
            <el-option label="全部" value=""></el-option>
            <el-option label="未上架" value="1"></el-option>
            <el-option label="已上架" value="2"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item>
          <el-input v-model="dataForm.title" placeholder="按标题查询" clearable></el-input>
        </el-form-item>
<!--        <el-form-item>-->
<!--          <el-button type="info" @click="exportHandle()">{{ $t('export') }}</el-button>-->
<!--        </el-form-item>-->
        <el-form-item>
          <el-button @click="getDataList2()">{{ $t('query') }}</el-button>
        </el-form-item>
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" @sort-change="dataListSortChangeHandle" style="width: 100%;">
        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
<!--        <el-table-column prop="id" label="ID" header-align="center" align="center"></el-table-column>-->
        <el-table-column prop="title" label="标题" header-align="center" align="center"></el-table-column>
<!--        <el-table-column prop="type" :formatter="formatType" label="类型" header-align="center" align="center"></el-table-column>-->
<!--        <el-table-column prop="canShare" label="是否可以分享: 1是, 2否" header-align="center" align="center"></el-table-column>-->
<!--        <el-table-column prop="uidId" label="合伙人ID（老师）" header-align="center" align="center"></el-table-column>-->
<!--        <el-table-column prop="intro" label="简介" header-align="center" align="center"></el-table-column>-->
<!--        <el-table-column prop="coverImgUrl" label="封面图片地址" header-align="center" align="center"></el-table-column>-->
<!--        <el-table-column prop="videos" label="视频列表"></el-table-column>-->
<!--        <el-table-column prop="videoNum" label="总视频数量" header-align="center" align="center"></el-table-column>-->
<!--        <el-table-column prop="videoTime" label="总视频时长" header-align="center" align="center"></el-table-column>-->
        <el-table-column prop="orders" sortable="custom" label="总销量" header-align="center" align="center"></el-table-column>
<!--        <el-table-column prop="province" label="省" header-align="center" align="center"></el-table-column>-->
<!--        <el-table-column prop="city" label="市" header-align="center" align="center"></el-table-column>-->
<!--        <el-table-column prop="tryExpireTime" label="试看时间(秒)" header-align="center" align="center"></el-table-column>-->
<!--        <el-table-column prop="buyExpireTime" label="购买有效期(天)" header-align="center" align="center"></el-table-column>-->
<!--        <el-table-column prop="showOrder" label="显示顺序（正序）" header-align="center" align="center"></el-table-column>-->
<!--        <el-table-column prop="playTimes" label="播放次数" header-align="center" align="center"></el-table-column>-->
<!--        <el-table-column prop="likeTimes" label="点赞次数" header-align="center" align="center"></el-table-column>-->
<!--        <el-table-column prop="markMoney" label="划线金额" header-align="center" align="center"></el-table-column>-->
        <el-table-column prop="money" label="金额" :formatter="cashConversion" header-align="center" align="center"></el-table-column>
<!--        <el-table-column prop="partnerProfitRate" label="合伙人收益比" header-align="center" align="center"></el-table-column>-->
<!--        <el-table-column prop="profitRate" label="一级收益比" header-align="center" align="center"></el-table-column>-->
<!--        <el-table-column prop="profitRate2" label="二级收益比" header-align="center" align="center"></el-table-column>-->
<!--        <el-table-column prop="profitRate3" label="三级收益比" header-align="center" align="center"></el-table-column>-->
<!--        <el-table-column prop="shopProfitRate" label="店铺收益比（1.0 - 合伙人 - 一级 - 二级 - 三级）" header-align="center" align="center"></el-table-column>-->
<!--        <el-table-column prop="orders" label="累计订单数量" header-align="center" align="center"></el-table-column>-->
<!--        <el-table-column prop="orderMoneys" label="累计订单销售金额" header-align="center" align="center"></el-table-column>-->
<!--        <el-table-column prop="plays" label="播放次数(真实)" header-align="center" align="center"></el-table-column>-->
<!--        <el-table-column prop="likes" label="点赞次数(真实)" header-align="center" align="center"></el-table-column>-->
<!--        <el-table-column prop="remark" label="备注" header-align="center" align="center"></el-table-column>-->
        <el-table-column prop="status" label="状态" :formatter="formatStatus" header-align="center" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.status === 1" size="small" type="success">未上架</el-tag>
            <el-tag v-if="scope.row.status === 2" size="small" >已上架</el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="createTime" sortable="custom" label="创建时间" header-align="center" align="center"></el-table-column>
        <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="200">
          <template slot-scope="scope">
            <el-button v-if="$hasPermission('av:course:update')" type="text" size="small" @click="addOrUpdate(scope.row.id)">{{ $t('update') }}</el-button>
<!--            <el-button v-if="$hasPermission('av:course:delete')" type="text" size="small" @click="deleteHandle(scope.row.id)">{{ $t('delete') }}</el-button>-->
            <el-button v-if="$hasPermission('av:course:update')" type="text" size="small" @click="buyHistory(scope.row.id)">购买记录</el-button>
            <el-button v-if="$hasPermission('av:course:update')" type="text" size="small" @click="dayMoneyStat(scope.row.id)">成交统计</el-button>
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
      <add-or-update-dialog ref="addOrUpdateDialog" @refreshDataList="getDataList"></add-or-update-dialog>
      <buy-history v-if="buyHistoryVisible" ref="buyHistory"></buy-history>
      <course-day-money-stat v-if="dayMoneyStatVisible" ref="CourseDayMoneyStat"></course-day-money-stat>
    </div>
  </el-card>
</template>

<script>
import mixinViewModule from '@/mixins/view-module'
import addOrUpdateDialog from './course-add-or-update-dialog'
import BuyHistory from './buy-history'
import CourseDayMoneyStat from './course-day-money-stat'
export default {
  components: {addOrUpdateDialog, BuyHistory,CourseDayMoneyStat},
  mixins: [mixinViewModule],
  data () {
    return {
      addOrUpdateDialogVisible: false,
      buyHistoryVisible: false,
      dayMoneyStatVisible: false,
      mixinViewModuleOptions: {
        getDataListURL: '/av/course/page',
        getDataListIsPage: true,
        exportURL: '/av/course/export',
        deleteURL: '/av/course',
        onURL: '/av/course/on',
        offURL: '/av/course/off',
        deleteIsBatch: true
      },
      dataForm: {
        id: '',
        status: ''
      }
    }
  },
  methods: {
    getDataList2() {
      this.page = 1
      this.getDataList()
    },

    cashConversion(row) {
      if(row.money == null){
        return 0.00
      }
      return row.money.toFixed(2);
    },
    addOrUpdate(id){
      this.addOrUpdateDialogVisible = true
      this.$nextTick(() => {
        this.$refs.addOrUpdateDialog.dataForm.id = id
        this.$refs.addOrUpdateDialog.init()
      })
    },
    onHandle(){
      if(!this.dataListSelections || this.dataListSelections.length === 0){
        return this.$message({
          message: '请选择上架项',
          type: 'warning',
          duration: 500
        })
      }
      this.$confirm('确定进行上架操作?', { 'handle': '上架' }, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$http.post(`${this.mixinViewModuleOptions.onURL}`,this.dataListSelections.map(item => item['id'])
        ).then(({ data: res }) => {
          if (res.code !== 0) {
            return this.$message.error(res.msg)
          }
          this.$message({
            message: this.$t('prompt.success'),
            type: 'success',
            duration: 500,
            onClose: () => {
              this.getDataList()
            }
          })
        })
      })
    },
    offHandle(){
      if(!this.dataListSelections || this.dataListSelections.length === 0){
        return this.$message({
          message: '请选择下架项',
          type: 'warning',
          duration: 500
        })
      }
      this.$confirm('确定进行下架操作?', { 'handle': '下架' }, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$http.post(`${this.mixinViewModuleOptions.offURL}`,this.dataListSelections.map(item => item['id'])
        ).then(({ data: res }) => {
          if (res.code !== 0) {
            return this.$message.error(res.msg)
          }
          this.$message({
            message: this.$t('prompt.success'),
            type: 'success',
            duration: 500,
            onClose: () => {
              this.getDataList()
            }
          })
        })
      })
    },
    //购买记录
    buyHistory(id){
      this.buyHistoryVisible = true
      this.$nextTick(() => {
        this.$refs.buyHistory.refId = id
        this.$refs.buyHistory.init()
      })
    },
    //每日成交额统计
    dayMoneyStat(id){
      this.dayMoneyStatVisible = true
      this.$nextTick(() => {
        this.$refs.CourseDayMoneyStat.dataForm.id = id
        this.$refs.CourseDayMoneyStat.init()
      })
    },
      formatType(row, column) {
          if(row.type == null){
            return ''
          }
          if(row.type === 1){
            return '视频'
          }
          return ''
      },
    //状态: 1未上架, 2已上架, 3删除
    formatStatus(row, column) {
      if(row.status == null){
        return ''
      }
      if(row.status === 1){
        return '未上架'
      }else if(row.status === 2){
        return '已上架'
      }else if(row.status === 3){
        return '删除'
      }else{
        return '未知'
      }
    }
  }}
</script>
