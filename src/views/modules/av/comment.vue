<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-av__comment}">
      <el-form :inline="true" :model="dataForm">
        <el-form-item>
          <el-input v-model="dataForm.kw" placeholder="按昵称、标题、订单编号" width="200" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-button @click="getDataList2()">{{ $t('query') }}</el-button>
        </el-form-item>
<!--        <el-form-item>-->
<!--          <el-button type="info" @click="exportHandle()">{{ $t('export') }}</el-button>-->
<!--        </el-form-item>-->
<!--        <el-form-item>-->
<!--          <el-button v-if="$hasPermission('av:comment:save')" type="primary" @click="addOrUpdateHandle()">{{ $t('add') }}</el-button>-->
<!--        </el-form-item>-->
<!--        <el-form-item>-->
<!--          <el-button v-if="$hasPermission('av:comment:delete')" type="danger" @click="deleteHandle()">{{ $t('deleteBatch') }}</el-button>-->
<!--        </el-form-item>-->
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
<!--        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>-->
<!--        <el-table-column prop="id" label="ID" header-align="center" align="center"></el-table-column>-->
        <el-table-column prop="orderNo" label="订单编号" header-align="center" align="center" width="160" fixed></el-table-column>
        <el-table-column prop="nickname" label="用户昵称" header-align="center" align="center" width="160"></el-table-column>
        <el-table-column prop="title" label="课程标题" header-align="center" align="center" width="180"></el-table-column>
<!--        <el-table-column prop="orderId" label="订单ID" header-align="center" align="center"></el-table-column>-->
        <el-table-column prop="status" label="状态" :formatter="commentType" header-align="center" align="center" width="160">
            <template slot-scope="scope">
                <el-tag v-if="scope.row.status === 1" size="small"  type="info">未审核</el-tag>
                <el-tag v-if="scope.row.status === 2" size="small" type="suncess">审核通过</el-tag>
                <el-tag v-if="scope.row.status === 3" size="small" type="danger">审核未通过</el-tag>
            </template>
        </el-table-column>
        <el-table-column prop="content" label="评论内容" header-align="center" align="center" width="180"></el-table-column>
        <el-table-column prop="reply" label="官方回复" header-align="center" align="center" width="180"></el-table-column>
        <el-table-column prop="star" label="星级" header-align="center" align="center" width="180" >
           <template slot-scope="scope">
             <el-rate
                     v-model="scope.row.star"
                     disabled
                     show-score
                     text-color="#ff9900"
                     score-template="{value}">
             </el-rate>
           </template>
        </el-table-column>
        <el-table-column prop="createTime" label="创建时间" header-align="center" align="center" width="160" ></el-table-column>
        <el-table-column prop="auditTime" label="审核时间" header-align="center" align="center" width="160" ></el-table-column>
        <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
          <template slot-scope="scope">
            <el-button v-if="$hasPermission('av:comment:update') && scope.row.status === 1" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">审核</el-button>
            <el-button v-if="scope.row.status !== 1" type="text" size="small"></el-button>
<!--            <el-button v-if="$hasPermission('av:comment:delete')" type="text" size="small" @click="deleteHandle(scope.row.id)">{{ $t('delete') }}</el-button>-->
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
import AddOrUpdate from './comment-add-or-update'
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/av/comment/page',
        getDataListIsPage: true,
        exportURL: '/av/comment/export',
        deleteURL: '/av/comment',
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
  methods:{
      getDataList2(){
        this.page = 1
        this.getDataList()
      },

    //状态：1.未审核  2.审核通过  3.审核未通过
    commentType(row,column) {
      if(row.status==null){
         return ''
      }
      if(row.status==1){
         return '未审核'
      }
      if(row.status==2){
        return '审核通过'
      }
      if(row.status==3){
        return '审核未通过'
      }
      else{
        return '未知'
      }
    }
  }
}
</script>
