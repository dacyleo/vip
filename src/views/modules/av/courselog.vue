<template>
    <el-card shadow="never" class="aui-card--fill">
        <div class="mod-av__courselog}">
            <el-form :inline="true" :model="dataForm">
                <el-form-item>
                    <el-input v-model="dataForm.kw" placeholder="根据昵称或标题查询" clearable></el-input>
                </el-form-item>
                <el-form-item label="记录类型">
                    <!--          记录类型: 1课程播放, 2课程访问, 3课程分享, 4课程点赞,5课程试看-->
                    <el-select v-model="dataForm.type" placeholder="记录类型" clearable>
                        <el-option label="全部" value=""></el-option>
                        <el-option label="课程播放" value="1"></el-option>
                        <el-option label="课程访问" value="2"></el-option>
                        <el-option label="课程分享" value="3"></el-option>
                        <el-option label="课程点赞" value="4"></el-option>
                        <el-option label="课程试看" value="5"></el-option>
                        <el-option label="首页访问" value="6"></el-option>
                    </el-select>
                </el-form-item>
                <el-form-item label="创建时间">
                    <el-date-picker
                            v-model="tempDate"
                            type="daterange"
                            range-separator="至"
                            start-placeholder="开始日期"
                            end-placeholder="结束日期">
                    </el-date-picker>
                </el-form-item>
                <el-form-item>
                    <el-button @click="getDataList2()">{{ $t('query') }}</el-button>
                </el-form-item>
                <el-form-item>
                    <el-button type="info" @click="exportHandle()">{{ $t('export') }}</el-button>
                </el-form-item>
                <!--        <el-form-item>-->
                <!--          <el-button v-if="$hasPermission('av:courselog:save')" type="primary" @click="addOrUpdateHandle()">{{ $t('add') }}</el-button>-->
                <!--        </el-form-item>-->
                <!--        <el-form-item>-->
                <!--          <el-button v-if="$hasPermission('av:courselog:delete')" type="danger" @click="deleteHandle()">{{ $t('deleteBatch') }}</el-button>-->
                <!--        </el-form-item>-->
            </el-form>
            <el-table v-loading="dataListLoading" :data="dataList" border
                      @selection-change="dataListSelectionChangeHandle" @sort-change="dataListSortChangeHandle"
                      style="width: 100%;">
                <!--        <el-table-column type="selection" header-align="center" align="center" width="50" ></el-table-column>-->
                <!--        <el-table-column prop="id" label="ID" header-align="center" align="center"></el-table-column>-->
                <el-table-column prop="title" label="课程标题" header-align="center" align="center"></el-table-column>
                <el-table-column prop="nickname" label="用户昵称" header-align="center" align="center"></el-table-column>
                <el-table-column prop="type" label="记录类型" :formatter="formatType" header-align="center" align="center">
                    <template slot-scope="scope">
                        <!--          课程访问类型: 1课程播放, 2课程访问, 3课程分享, 4课程点赞, 5 课程试看, 6 首页访问-->
                        <el-tag v-if="scope.row.type === 1" size="small" type="info">课程播放</el-tag>
                        <el-tag v-if="scope.row.type === 2" size="small" type="warning">课程访问</el-tag>
                        <el-tag v-if="scope.row.type === 3" size="small">课程分享</el-tag>
                        <el-tag v-if="scope.row.type === 4" size="small" type="danger">课程点赞</el-tag>
                        <el-tag v-if="scope.row.type === 5" size="small" type="success">课程试看</el-tag>
                        <el-tag v-if="scope.row.type === 6" size="small" id="homeVisite">首页访问</el-tag>
                    </template>
                </el-table-column>
                <!--        <el-table-column prop="orderId" label="订单ID" header-align="center" align="center"></el-table-column>-->
                <el-table-column prop="ip" label="IP" header-align="center" align="center"></el-table-column>
                <!--        <el-table-column prop="userAgent" label="User-Agent" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="remark" label="备注" header-align="center" align="center"></el-table-column>-->
                <el-table-column prop="createTime" sortable="custom" label="创建时间" header-align="center"
                                 align="center"></el-table-column>
                <!--        <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">-->
                <!--          <template slot-scope="scope">-->
                <!--            <el-button v-if="$hasPermission('av:courselog:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">{{ $t('update') }}</el-button>-->
                <!--            <el-button v-if="$hasPermission('av:courselog:delete')" type="text" size="small" @click="deleteHandle(scope.row.id)">{{ $t('delete') }}</el-button>-->
                <!--          </template>-->
                <!--        </el-table-column>-->
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
<style>
    #homeVisite{
        background-color: #ffc0cb33;
        border-color: #ffc0cb61;
        color: #f88ca0;
    }
</style>
<script>
    import mixinViewModule from '@/mixins/view-module'
    import AddOrUpdate from './courselog-add-or-update'

    export default {
        mixins: [mixinViewModule],
        data() {
            return {
                mixinViewModuleOptions: {
                    getDataListURL: '/av/courselog/page',
                    getDataListIsPage: true,
                    exportURL: '/av/courselog/export',
                    deleteURL: '/av/courselog',
                    deleteIsBatch: true
                },
                tempDate: [],
                dataForm: {
                    id: '',
                    kw: '',
                    type: '',
                    startTime: '',
                    endTime: ''
                }
            }
        },
        components: {
            AddOrUpdate
        },
        methods: {
            getDataList2() {
                this.page = 1
                if (this.tempDate && this.tempDate.length > 0) {
                    this.dataForm.startTime = this.$moment(this.tempDate[0]).format("YYYY-MM-DD")
                    this.dataForm.endTime = this.$moment(this.tempDate[1]).format("YYYY-MM-DD")
                }
                this.getDataList()
            },
            // 课程访问类型: 1课程播放, 2课程访问, 3课程分享, 4课程点赞, 5 课程试看, 6 首页访问
            formatType(row) {
                if (row.type == null) {
                    return ''
                }
                if (row.type === 1) {
                    return '课程播放'
                } else if (row.type === 2) {
                    return '课程访问'
                } else if (row.type === 3) {
                    return '课程分享'
                } else if (row.type === 4) {
                    return '课程点赞'
                } else if (row.type === 5) {
                    return '课程试看'
                } else if (row.type === 6) {
                    return '首页访问'
                } else {
                    return '未知'
                }
            }
        }
    }
</script>
