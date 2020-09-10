<template>
    <el-dialog width="70%" :visible.sync="visible" v-if="dataForm.type === 1" title="订单记录" :close-on-click-modal="true"
               :close-on-press-escape="true" @close="inputEmpty">
        <el-card shadow="never" class="aui-card--fill">
            <div class="mod-av__user}">
                <el-form :inline="true" :model="dataForm">
                    <el-form-item>
                        <el-input v-model="dataForm.kw" placeholder="按订单编号或课程标题" clearable id="messageInput"></el-input>
                    </el-form-item>
                    <el-form-item>
                        <el-button @click="getDataList2()">{{ $t('query') }}</el-button>
                    </el-form-item>
                </el-form>
                <el-table v-loading="dataListLoading" :data="dataList" border style="width: 100%;" @sort-change="dataListSortChangeHandle">
                    <!--                    <el-table-column prop="id" label="ID" header-align="center" align="center"></el-table-column>-->
                    <el-table-column prop="orderNo" label="订单编号" header-align="center" align="center"></el-table-column>
                    <!--                    <el-table-column prop="uid" label="用户id" header-align="center" align="center"></el-table-column>-->
                    <!--                    <el-table-column prop="nickname" label="昵称" header-align="center" align="center"></el-table-column>-->
                    <!--                    <el-table-column label="头像" header-align="center" align="center">-->
                    <!--                        <template slot-scope="scope">-->
                    <!--                            <img :src="scope.row.headImgUrl" width="40" height="40" class="head_pic"/>-->
                    <!--                        </template>-->
                    <!--                    </el-table-column>-->
                    <!--                    <el-table-column prop="phone" label="手机号码" header-align="center" align="center"></el-table-column>-->
                    <el-table-column prop="title" label="课程标题" header-align="center" align="center"></el-table-column>
                    <el-table-column prop="payStatus" label="支付状态" :formatter="defrayStatus" header-align="center"
                                     align="center"></el-table-column>
                    <el-table-column prop="payMoney" :formatter="payMoneyKeep" label="订单金额" header-align="center"
                                     align="center"></el-table-column>
                    <el-table-column prop="createTime" sortable="custom" label="购买时间" header-align="center"
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

    <el-dialog width="70%" :visible.sync="visible" v-else-if="dataForm.type === 2" title="赠送记录"
               :close-on-click-modal="true" :close-on-press-escape="true"  @close="inputEmpty">
        <el-card shadow="never" class="aui-card--fill">
            <div class="mod-av__user}">
                <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
                    <el-form-item>
                        <el-input v-model="dataForm.kw" placeholder="订单编号或课程标题" clearable></el-input>
                    </el-form-item>
                    <el-form-item>
                        <el-button @click="getDataList()">{{ $t('query') }}</el-button>
                    </el-form-item>
                </el-form>
                <el-table v-loading="dataListLoading" :data="dataList" border style="width: 100%;" @sort-change="dataListSortChangeHandle">
<!--                    <el-table-column prop="id" label="ID" header-align="center" align="center"></el-table-column>-->
                    <el-table-column prop="orderNo" label="订单编号" header-align="center" align="center"></el-table-column>
                    <el-table-column prop="title" label="课程标题" header-align="center" align="center"></el-table-column>
                    <el-table-column prop="playStatus" :formatter="playStatusSetUp" label="播放状态" header-align="center" align="center">
                        <template slot-scope="scope">
                            <el-tag v-if="scope.row.playStatus === 1" size="small"  type="danger">不可播放</el-tag>
                            <el-tag v-if="scope.row.playStatus === 2" size="small"  type="success">可以播放</el-tag>
                            <el-tag v-if="scope.row.playStatus === 3" size="small"  type="info">已过期</el-tag>
                        </template>
                    </el-table-column>

<!--                    <el-table-column prop="uid" label="用户id" header-align="center" align="center"></el-table-column>-->
<!--                    <el-table-column prop="nickname" label="昵称" header-align="center" align="center"></el-table-column>-->
<!--                    <el-table-column label="头像" header-align="center" align="center">-->
<!--                        <template slot-scope="scope">-->
<!--                            <img :src="scope.row.headImgUrl" width="40" height="40" class="head_pic"/>-->
<!--                        </template>-->
<!--                    </el-table-column>-->
<!--                    <el-table-column prop="phone" label="手机号码" header-align="center" align="center"></el-table-column>-->

<!--                    <el-table-column prop="payStatus" label="支付状态" :formatter="defrayStatus" header-align="center"-->
<!--                                     align="center"></el-table-column>-->

                    <el-table-column prop="createTime" sortable="custom" label="赠送时间" header-align="center"
                                     align="center"></el-table-column>
                    <el-table-column prop="playExpireTime"  label="播放到期" header-align="center"
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
                kw:'',
                mixinViewModuleOptions: {
                    activatedIsNeed: true,
                    getDataListURL: '/av/order/findListByUidAndTypePage',
                    getDataListIsPage: true
                },
                userId: '',
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
                this.dataForm.id = this.userId
                this.dataForm.type = this.type
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
            //播放状态: 1不可播放, 2可以播放, 3已过期
            playStatusSetUp(row,column) {
                if (row.playStatus === null){
                    return ''
                }
                if (row.playStatus === 1){
                    return '不可播放'
                }
                if (row.playStatus === 2){
                    return '可以播放'
                }
                if (row.playStatus === 3){
                    return '已过期'
                }
            },

            payMoneyKeep(row, column) {
                // if (row.payMoney){
                //     return row.payMoney.toFixed(2)
                // }
                // else{
                //     return 0.00
                // }
                if (row.payMoney === null){
                    return 0.00
                }
                return row.payMoney.toFixed(2);
            },
            inputEmpty(){
                this.dataForm.kw=''
            }


        }
    }
</script>
