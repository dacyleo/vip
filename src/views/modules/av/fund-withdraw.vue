<template>
    <el-card shadow="never" class="aui-card--fill">
        <div class="mod-av__fund}">
            <el-form :inline="true" :model="dataForm">
                <el-form-item label="状态">
                    <!--         1未审核, 2审核通过, 3审核不通过-->
                    <el-select v-model="dataForm.status" placeholder="状态" clearable>
                        <el-option label="全部" value=""></el-option>
                        <el-option label="未审核" value="1"></el-option>
                        <el-option label="审核通过" value="2"></el-option>
                        <el-option label="审核不通过" value="3"></el-option>
                    </el-select>
                </el-form-item>
                <el-form-item>
                    <el-input v-model="dataForm.kw" placeholder="根据昵称查询" clearable></el-input>
                </el-form-item>
                <el-form-item>
                    <el-button @click="getDataList2()">{{ $t('query') }}</el-button>
                </el-form-item>
                        <el-form-item>
                          <el-button type="info" @click="exportHandle()">{{ $t('export') }}</el-button>
                        </el-form-item>
            </el-form>
            <el-table v-loading="dataListLoading" :data="dataList" border
                      style="width: 100%;">
                <el-table-column prop="nickname" label="用户昵称" header-align="center" align="center"></el-table-column>
                <el-table-column prop="status" label="状态" :formatter="fundStatus" header-align="center" align="center">
                     <template slot-scope="scope">
                         <el-tag v-if="scope.row.status == 1" size="small">未审核</el-tag>
                         <el-tag v-if="scope.row.status == 2" size="small" type="success">审核通过</el-tag>
                         <el-tag v-if="scope.row.status == 3" size="small" type="danger">审核不通过</el-tag>
                     </template>
                </el-table-column>
                <el-table-column prop="money" label="金额" :formatter="formatMoney" header-align="center" align="center"></el-table-column>
                <el-table-column prop="moneyStatus" label="资金状态" :formatter="moneyStatus" header-align="center"
                                 align="center"></el-table-column>
                <el-table-column prop="remark" label="备注" header-align="center" align="center"></el-table-column>
                <el-table-column prop="createTime" label="创建时间" header-align="center" align="center"></el-table-column>
                <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
                    <template slot-scope="scope">
                        <el-button v-if="scope.row.type == 1 && scope.row.status == 1" type="text" size="small"
                                   @click="withdraw(scope.row.id)">提现审核
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
            <!-- 弹窗, 提现审核 -->
            <withdraw-audit v-if="withdrawAuditVisible" @refreshList="refresh" ref="withdrawAudit"></withdraw-audit>
        </div>
    </el-card>
</template>

<script>
    import mixinViewModule from '@/mixins/view-module'
    import withdrawAudit from './withdraw-audit'

    export default {
        mixins: [mixinViewModule],
        data() {
            return {
                mixinViewModuleOptions: {
                    getDataListURL: '/av/fund/page',
                    getDataListIsPage: true,
                    exportURL: '/av/fund/export',
                    deleteURL: '/av/fund',
                    deleteIsBatch: true
                },
                dataForm: {
                    kw: '',
                    type: 1,
                    status: ''
                },
                withdrawAuditVisible: false
            }
        },
        components: {
            withdrawAudit
        },

        methods: {
            getDataList2() {
                this.page = 1
                this.getDataList()
            },
            formatMoney(item){
                return item.money.toFixed(2)
            },
            refresh(){
                this.getDataList()
            },
            withdraw(id) {
                this.withdrawAuditVisible = true
                this.$nextTick(() => {
                    this.$refs.withdrawAudit.dataForm.id = id
                    this.$refs.withdrawAudit.init()
                })
            },
            //状态： 1未审核, 2审核通过, 3审核不通过
            fundStatus(row, column) {
                if (row.status === null) {
                    return ''
                }
                if (row.status === 1) {
                    return '未审核'
                }
                if (row.status === 2) {
                    return '审核通过'
                }
                if (row.status === 3) {
                    return '审核不通过'
                } else {
                    return '未知'
                }
            },
            //资金状态  1.未处理  2.已处理
            moneyStatus(row, column) {
                if (row.moneyStatus == null) {
                    return ''
                }
                if (row.moneyStatus == 1) {
                    return '未到账'
                } else if (row.moneyStatus == 2) {
                    return '已到账'
                } else {
                    return '未知'
                }
            }
        }
    }
</script>
