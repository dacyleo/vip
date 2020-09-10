<template>
    <el-card shadow="never" class="aui-card--fill">
        <div class="mod-av__fund}">
            <el-form :inline="true" :model="dataForm">
                <el-form-item>
                    <el-input v-model="dataForm.kw" placeholder="根据昵称查询" clearable></el-input>
                </el-form-item>
                <el-form-item>
                    <el-button @click="getDataList2()">{{ $t('query') }}</el-button>
                </el-form-item>
                <!--        <el-form-item>-->
                <!--          <el-button type="info" @click="exportHandle()">{{ $t('export') }}</el-button>-->
                <!--        </el-form-item>-->

                <!--        <el-form-item>-->
                <!--          <el-button v-if="$hasPermission('av:fund:delete')" type="danger" @click="deleteHandle()">{{ $t('deleteBatch') }}</el-button>-->
                <!--        </el-form-item>-->
            </el-form>
            <el-table v-loading="dataListLoading" :data="dataList" border
                      @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
<!--                <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>-->
                <el-table-column prop="id" label="ID" header-align="center" align="center"></el-table-column>
                <el-table-column prop="nickname" label="用户昵称" header-align="center" align="center"></el-table-column>
                <el-table-column prop="type" label="资金类型" :formatter="moneyType" header-align="center"
                                 align="center"></el-table-column>
                <el-table-column prop="status" label="状态" :formatter="fundStatus" header-align="center"
                                 align="center"></el-table-column>
<!--                <el-table-column prop="orderId" label="订单ID" header-align="center" align="center"></el-table-column>-->
                <el-table-column prop="money" label="金额" :formatter="formatMoney" header-align="center" align="center"></el-table-column>
                <!--        <el-table-column prop="content" label="内容" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="remark" label="备注" header-align="center" align="center"></el-table-column>-->
                <el-table-column prop="moneyStatus" label="资金状态" :formatter="formatMoneyStatus" header-align="center"
                                 align="center"></el-table-column>
                <el-table-column prop="createTime" label="创建时间" header-align="center" align="center"></el-table-column>
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
</template>

<script>
    import mixinViewModule from '@/mixins/view-module'

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
                    kw: ''
                }
            }
        },
        components: {

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
            //资金类型: 1提现, 2退款, 3工资发放(不需要申请提现、账户里面的钱不会减扣)
            moneyType(row, column) {
                if (row.type === null) {
                    return ''
                }
                if (row.type === 1) {
                    return '提现'
                }
                if (row.type === 2) {
                    return '退款'
                }
                if (row.type === 3) {
                    return '工资发放'
                } else {
                    return '未知'
                }
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
            formatMoneyStatus(row, column) {
                if (row.moneyStatus == null) {
                    return ''
                }
                if (row.moneyStatus == 1) {
                    return '未处理'
                } else if (row.moneyStatus == 2) {
                    return '已处理'
                } else {
                    return '未知'
                }
            }
        }
    }
</script>
