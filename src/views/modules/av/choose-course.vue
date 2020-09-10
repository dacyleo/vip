<template>
    <el-dialog :visible.sync="visible" width="70%" title="课程列表" :close-on-click-modal="true" :close-on-press-escape="true" @close="inputEmpty">
        <el-card shadow="never" class="aui-card--fill">
            <div class="mod-av__user}">
                <el-form :inline="true" :model="dataForm">
                    <el-form-item>
                        <el-input v-model="dataForm.title" placeholder="按标题查询" clearable></el-input>
                    </el-form-item>
                    <el-form-item>
                        <el-button @click="getDataList2()">{{ $t('query') }}</el-button>
                    </el-form-item>
                </el-form>
                <el-table v-loading="dataListLoading" :data="dataList" border style="width: 100%;">
<!--                    <el-table-column prop="id" label="ID" header-align="center" align="center"></el-table-column>-->
                    <el-table-column prop="title" label="标题" header-align="center" align="center"></el-table-column>
                    <el-table-column prop="uidName" label="合伙人" header-align="center" align="center"></el-table-column>
                    <el-table-column prop="money" label="金额" :formatter="formatMoney" header-align="center" align="center"></el-table-column>
                    <el-table-column prop="createTime" label="创建时间" header-align="center"
                                     align="center"></el-table-column>
                    <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center"
                                     width="150">
                        <template slot-scope="scope">
                            <el-button type="text" size="small" @click="choose(scope.row.id)">确定
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
            </div>
        </el-card>
    </el-dialog>
</template>

<script>
    import mixinViewModule from '@/mixins/view-module'

    export default {
        mixins: [mixinViewModule],
        data() {
            return {
                uid: '',
                visible: false,
                mixinViewModuleOptions: {
                    getDataListURL: '/av/course/givingCourses',
                    getDataListIsPage: true
                },
                dataForm: {
                    title: ''
                }
            }
        },
        methods: {
            getDataList2() {
                this.page = 1
                this.getDataList()
            },
            formatMoney(row){
                return row.money.toFixed(2)
            },
            init() {
                this.visible = true
                this.getDataList()
            },
            choose(id) {
                let that = this
                this.$http.post("/av/user/giveCourse",{"uid":this.uid,"courseId":id}).then(function (res) {
                    if (res.data.code === 0) {
                        that.$message.info("赠送成功")
                        that.visible = false
                    } else {
                        return that.$message.error(res.data.msg)
                    }
                }, function (error) {
                    return that.$message.error("错误")
                })
            },
            inputEmpty(){
                this.dataForm.kw=''
            }
        }
    }
</script>
