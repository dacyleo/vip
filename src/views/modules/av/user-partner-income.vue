<template>
    <el-dialog width="70%"  :visible.sync="visible" v-if="this.type === 6" title="合伙人收益" :close-on-click-modal="true"
               :close-on-press-escape="true" @close="inputEmpty">
        <el-card   shadow="never" class="aui-card--fill">
            <div class="mod-av__user}">
                <el-form :inline="true" :model="dataForm">
                    <el-form-item>
                        <el-input v-model="dataForm.kw" placeholder="按订单编号" clearable></el-input>
                    </el-form-item>
                    <el-form-item>
                        <el-button @click="getDataList2()">{{ $t('query') }}</el-button>
                    </el-form-item>
                </el-form>
                <el-table v-loading="dataListLoading" :data="dataList" border style="width: 100%;">
                    <!--                    <el-table-column prop="id" label="ID" header-align="center" align="center"></el-table-column>-->
                    <el-table-column prop="orderNo" label="订单编号" width="200" header-align="center" align="center"></el-table-column>
                    <el-table-column prop="title" label="课程标题" header-align="center" align="center"></el-table-column>
                    <el-table-column prop="money"  label="金额" :formatter="moneyKeep"  header-align="center" align="center"></el-table-column>
<!--                    <el-table-column prop="beforeBalance" label="用户余额(交易前)" :formatter="defrayStatus" header-align="center" align="center"></el-table-column>-->
<!--                    <el-table-column prop="profit"   label="一级收益"  header-align="center" align="center"></el-table-column>-->
                    <el-table-column prop="level"  label="收益等级" :formatter="earningsLevel"  header-align="center" align="center"></el-table-column>
                    <el-table-column prop="remark"  label="备注"  header-align="center" align="center"></el-table-column>
                    <el-table-column prop="type"  label="收益类型" :formatter="earningsType"  header-align="center" align="center"></el-table-column>
                    <el-table-column prop="createTime" label="创建时间" width="180" sortable="custom" header-align="center"
                                     align="center"></el-table-column>
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

    <el-dialog width="70%" :visible.sync="visible" v-else-if="this.type === 5" title="收益明细" :close-on-click-modal="true" :close-on-press-escape="true" @close="inputEmpty">
        <el-card shadow="never" class="aui-card--fill" >
            <div class="mod-av__user}">
                <el-form :inline="true" :model="dataForm">
                    <el-form-item>
                        <el-input v-model="dataForm.kw" placeholder="按订单编号" clearable></el-input>
                    </el-form-item>
                    <el-form-item>
                        <el-button @click="getDataList2()">{{ $t('query') }}</el-button>
                    </el-form-item>
                </el-form>
                <el-table v-loading="dataListLoading" :data="dataList" border style="width: 100%;" @sort-change="dataListSortChangeHandle">
<!--                    <el-table-column prop="nickname" label="昵称" header-align="center" align="center"></el-table-column>-->
<!--                    <el-table-column label="头像" header-align="center" align="center">-->
<!--                        <template slot-scope="scope">-->
<!--                            <img :src="scope.row.headImgUrl" width="40" height="40" class="head_pic"/>-->
<!--                        </template>-->
<!--                    </el-table-column>-->
                    <el-table-column prop="orderNo" label="订单编号" width="200" header-align="center" align="center"></el-table-column>
                    <el-table-column prop="title" label="课程标题" header-align="center" align="center" width="170"></el-table-column>
                    <el-table-column prop="money"  label="金额" :formatter="moneyKeep"  header-align="center" align="center"></el-table-column>

<!--                    <el-table-column prop="beforeBalance" label="用户余额(交易前)" :formatter="defrayStatus" header-align="center" align="center"></el-table-column>-->
                    <!--                    <el-table-column prop="profit"   label="一级收益"  header-align="center" align="center"></el-table-column>-->
<!--                    <el-table-column prop="balance"  label="用户余额" :formatter="afterBalance"  header-align="center" align="center"></el-table-column>-->
                    <el-table-column prop="level"  label="收益等级" :formatter="earningsLevel"  header-align="center" align="center"></el-table-column>
                    <el-table-column prop="remark"  label="备注"  header-align="center" align="center"></el-table-column>
                    <el-table-column prop="type"  label="收益类型" :formatter="earningsType"  header-align="center" align="center"></el-table-column>
                    <el-table-column prop="createTime" label="创建时间" width="180" sortable="custom" header-align="center"
                                     align="center"></el-table-column>
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
                visible: false,
                mixinViewModuleOptions: {
                    activatedIsNeed: true,
                    getDataListURL: '/av/trade/findTradeByUidAndType',
                    getDataListIsPage: true
                },
                id: '',
                type: '',
                dataForm: {
                    id: '',
                    type: '',
                    kw:''
                }
            }
        },

        methods: {
            getDataList2() {
                this.page = 1
                this.getDataList()
            },
            init() {
                this.dataForm.id = this.id
                if(this.type === 5){
                    this.dataForm.type = 3
                }
                if(this.type === 6){
                    this.dataForm.type = 2
                }
                this.visible = true
                this.getDataList()
            },

            //用户类型: 1普通用户, 2合伙人
            formatType(row) {
                switch (row.type) {
                    case 1 :
                        return '普通订单'
                    case 2 :
                        return '赠送订单'
                    default :
                        return '未知'
                }
            },
            //支付状态: 1未支付, 2已支付, 3已退款
            defrayStatus(row, column) {
                return row.beforeBalance.toFixed(2)
                if (row.payStatus == null) {
                    return ''
                }
                if (row.payStatus == 1) {
                    return '未支付'
                }
                if (row.payStatus == 2) {
                    return '已支付'
                }
                if (row.payStatus == 3) {
                    return '已退款'
                }
            },
            moneyKeep(row,column) {
                if (row.money == null){
                    return 0.00
                }
                return row.money.toFixed(2)
            },
            afterBalance (row,column){
                if (row.balance == null){
                    return 0.00
                }
                return row.balance.toFixed(2)
            },
            //收益等级：0 无等级, 1 一级收益, 2 二级收益 3 三级收益
            earningsLevel(row,column) {
                if(row.level === null){
                    return ''
                }
                if(row.level === 0){
                    return '-'
                }
                if(row.level === 1){
                    return '一级收益'
                }
                if(row.level === 2){
                    return '二级收益'
                }
                if(row.level === 3){
                    return '三级收益'
                }
            },
            //收益类型:1课程购买, 2合伙人收益, 3推广收益, 4店铺收益, 5提现, 6退款, 7工资发放
            earningsType (row, column) {
                if(row.type === null){
                    return ''
                }
                if(row.type === 1){
                    return '课程购买'
                }
                if(row.type === 2){
                    return '合伙人收益'
                }
                if(row.type === 3){
                    return '推广收益'
                }
                if(row.type === 4){
                    return '店铺收益'
                }
                if(row.type === 5){
                    return '提现'
                }
                if(row.type === 6){
                    return '退款'
                }
                if(row.type === 7){
                    return '工资发放'
                }
            },
            inputEmpty(){
                this.dataForm.kw=''
            }



        }
    }
</script>
