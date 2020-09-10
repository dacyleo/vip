<template>
    <el-card shadow="never" class="aui-card--fill">
        <div class="mod-av__fund}">
            <el-form :inline="true" :model="dataForm">
                <el-form-item>
                    <el-button v-if="$hasPermission('av:fund:save')" type="primary" @click="salaryAdd()">新增工资</el-button>
                </el-form-item>
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
                        <!--            <el-button v-if="$hasPermission('av:fund:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">{{ $t('update') }}</el-button>-->
                        <!--            <el-button v-if="$hasPermission('av:fund:delete')" type="text" size="small" @click="deleteHandle(scope.row.id)">{{ $t('delete') }}</el-button>-->
                        <el-button v-if="$hasPermission('fund:salary:audit') && scope.row.type == 3 && scope.row.status == 1" type="text" size="small"
                                   @click="salary(scope.row.id,scope.row.remark)">工资审核
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
            </el-form>
            <!-- 弹窗, 新增工资 -->
            <salary-add v-if="salaryAddVisible" @refreshList="refresh" ref="salaryAdd"></salary-add>
            <!-- 弹窗, 工资审核 -->
            <salary-audit v-if="salaryAuditVisible" @refreshList="refresh" ref="salaryAudit"></salary-audit>
        </div>
    </el-card>
</template>

<script>
    import mixinViewModule from '@/mixins/view-module'
    import salaryAdd from './salary-add'
    import salaryAudit from './salary-audit'

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
                    type: 3,
                    status: ''
                },
                salaryAddVisible: false,
                salaryAuditVisible: false
            }
        },
        components: {
            salaryAdd,
            salaryAudit
        },
        methods: {
            getDataList2(){
                this.page = 1
                this.getDataList()
            },
            refresh(){
                this.getDataList()
            },
            salary(id,remark) {
                this.salaryAuditVisible = true
                this.$nextTick(() => {
                    this.$refs.salaryAudit.dataForm.id = id
                    this.$refs.salaryAudit.dataForm.remark = remark
                    this.$refs.salaryAudit.init()
                })
            },
            salaryAdd(){
                this.salaryAddVisible = true
                this.$nextTick(() => {
                    this.$refs.salaryAdd.init()
                })
            },
            formatMoney(item){
                return item.money.toFixed(2)
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
