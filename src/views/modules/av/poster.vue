<template>
    <el-card shadow="never" class="aui-card--fill">
        <div class="mod-av__poster}">
            <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
                <!--        <el-form-item>-->
                <!--          <el-input v-model="dataForm.id" placeholder="id" clearable></el-input>-->
                <!--        </el-form-item>-->
                <!--        <el-form-item>-->
                <!--          <el-button @click="getDataList()">{{ $t('query') }}</el-button>-->
                <!--        </el-form-item>-->
                <!--        <el-form-item>-->
                <!--          <el-button type="info" @click="exportHandle()">{{ $t('export') }}</el-button>-->
                <!--        </el-form-item>-->
                <el-form-item>
                    <el-button v-if="$hasPermission('av:poster:save')" type="primary" @click="addOrUpdateHandle()">{{
                        $t('add') }}
                    </el-button>
                </el-form-item>
                <el-form-item>
                    <el-button v-if="$hasPermission('av:course:save')" type="warning" @click="onHandle()">上架</el-button>
                </el-form-item>
                <el-form-item>
                    <el-button v-if="$hasPermission('av:course:save')" type="warning" @click="offHandle()">下架
                    </el-button>
                </el-form-item>
                <el-form-item>
                    <el-button v-if="$hasPermission('av:poster:delete')" type="danger" @click="deleteHandle()">{{
                        $t('deleteBatch') }}
                    </el-button>
                </el-form-item>
            </el-form>
            <el-table v-loading="dataListLoading" :data="dataList" border
                      @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
                <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
                <!--        <el-table-column prop="id" label="ID" header-align="center" align="center"></el-table-column>-->
                <el-table-column prop="imgUrl" label="背景图片" header-align="center" align="center">
                    <template slot-scope="scope">
                        <img :src="scope.row.imgUrl" width="100"class="head_pic"/>
                    </template>
                </el-table-column>
                <!--        <el-table-column prop="posterId" label="海报生成器对应的id" header-align="center" align="center"></el-table-column>-->
                <el-table-column prop="type" label="类型" :formatter="posterType" header-align="center"
                                 align="center"></el-table-column>
                <el-table-column prop="status" label="状态" :formatter="posterStatus" header-align="center" align="center">
                  <template slot-scope="scope">
                    <el-tag v-if="scope.row.status == 1" size="samll" type="success">未上架</el-tag>
                    <el-tag v-if="scope.row.status == 2" size="samll">已上架</el-tag>
                    <el-tag v-if="scope.row.status == 3" size="samll" type="danger">已删除</el-tag>
                  </template>
                </el-table-column>
                <el-table-column prop="title" label="标题" header-align="center" align="center"></el-table-column>
                <el-table-column prop="showOrder" label="显示顺序" header-align="center" align="center"></el-table-column>
                <el-table-column prop="remark" label="备注" header-align="center" align="center"></el-table-column>
                <el-table-column prop="createTime" label="创建时间" header-align="center" align="center" width="160"></el-table-column>
                <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
                    <template slot-scope="scope">
                        <el-button v-if="$hasPermission('av:poster:update')" type="text" size="small"
                                   @click="addOrUpdateHandle(scope.row.id)">{{ $t('update') }}
                        </el-button>
                        <el-button v-if="$hasPermission('av:poster:delete')" type="text" size="small"
                                   @click="deleteHandle(scope.row.id)">{{ $t('delete') }}
                        </el-button>
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
    import AddOrUpdate from './poster-add-or-update'

    export default {
        mixins: [mixinViewModule],
        data() {
            return {
                mixinViewModuleOptions: {
                    getDataListURL: '/av/poster/page',
                    getDataListIsPage: true,
                    exportURL: '/av/poster/export',
                    deleteURL: '/av/poster',
                    onURL: '/av/poster/grounding',
                    offURL: '/av/poster/undercarriage',
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
            this.$confirm('确定进行上架操作?', {'handle': '上架'}, '提示', {
              confirmButtonText: '确定',
              cancelButtonText: '取消',
              type: 'warning'
            }).then(() => {
              this.$http.post(`${this.mixinViewModuleOptions.onURL}`, this.dataListSelections.map(item => item['id'])
              ).then(({data: res}) => {
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
          //下架
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

            //类型  1 有头像 , 2 无头像
            posterType(row, column) {
                if (row.type == null) {
                    return ''
                }
                if (row.type == 1) {
                    return '有头像'
                }
                if (row.type == 2) {
                    return '无头像'
                }
            },
            //状态 1未上架, 2已上架, 3删除
            posterStatus(row, column) {
                if (row.status == null) {
                    return ''
                }
                if (row.status == 1) {
                    return '未上架'
                }
                if (row.status == 2) {
                    return '已上架'
                }
                if (row.status == 3) {
                    return '删除'
                }
            }
        }
    }
</script>
