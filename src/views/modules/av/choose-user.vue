<template>
  <el-dialog :visible.sync="visible" :modal="false" title="选择合伙人" :close-on-click-modal="true" :close-on-press-escape="true">
    <el-card shadow="never" class="aui-card--fill">
      <div class="mod-av__user}">
        <el-form :inline="true" :model="dataForm">
          <el-form-item>
            <el-input v-model="dataForm.kw" placeholder="按昵称和手机号查询" clearable></el-input>
          </el-form-item>
          <el-form-item>
            <el-button @click="getDataList2()">{{ $t('query') }}</el-button>
          </el-form-item>
        </el-form>
        <el-table v-loading="dataListLoading" :data="dataList" border style="width: 100%;">
          <el-table-column prop="id" label="ID" header-align="center" align="center"></el-table-column>
          <el-table-column prop="phone" label="手机号码" header-align="center" align="center"></el-table-column>
          <el-table-column prop="nickname" label="昵称" header-align="center" align="center"></el-table-column>
          <el-table-column label="头像" header-align="center" align="center">
              <template slot-scope="scope">
                <img :src="scope.row.headImgUrl" width="40" height="40" class="head_pic"/>
              </template>
          </el-table-column>
          <el-table-column prop="sex" label="性别" header-align="center" :formatter="formatSex" align="center"></el-table-column>
          <el-table-column prop="createTime" label="创建时间" header-align="center" align="center"></el-table-column>
          <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
              <template slot-scope="scope">
                <el-button type="text" size="small" @click="choose(scope.row.id,scope.row.nickname)">确定</el-button>
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
      visible: false,
      mixinViewModuleOptions: {
        getDataListURL: '/av/user/chooseUserPage',
        getDataListIsPage: true
      },
      dataForm: {
        kw: ''
      }
    }
  },
  methods: {
    getDataList2(){
      this.page = 1
      this.getDataList()
    },
    init () {
      this.visible = true
      this.getDataList()
    },
    choose: function(id,nickname){
      this.$emit('getUser',{"id":id,"nickname":nickname})
      this.visible = false
    },
    //性别: 1表示男性, 2表示女性
    formatSex(row){
      switch (row.sex) {
        case 1 :
          return '男性'
        case 2 :
          return '女性'
        default :
          return '未知'
      }
    }
  }
}
</script>
