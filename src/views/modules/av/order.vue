<template>
    <el-card shadow="never" class="aui-card--fill">
        <div class="mod-av__order}">
            <el-form :inline="true" :model="dataForm">
                <el-form-item>
                    <el-input v-model="dataForm.kw" style="width:250px" placeholder="根据昵称、标题、订单号查询"
                              clearable></el-input>
                </el-form-item>

                <el-form-item>
                    订单类型:
                    <el-select v-model="dataForm.type" placeholder="订单类型" clearable >
                        <el-option label="全部" value=""></el-option>
                        <el-option label="普通订单" value="1"></el-option>
                        <el-option label="赠送订单" value="2"></el-option>
                    </el-select>
                </el-form-item>

                <el-form-item>
                    订单状态:
                    <el-select v-model="dataForm.status" placeholder="订单状态" clearable>
                        <el-option label="全部" value=""></el-option>
                        <el-option label="待付款" value="1"></el-option>
                        <el-option label="待发货" value="2"></el-option>
                        <el-option label="待评价" value="3"></el-option>
                        <el-option label="已完成" value="4"></el-option>
                        <el-option label="售后" value="5"></el-option>
                    </el-select>
                </el-form-item>

                <el-form-item>
                      售后状态:
                        <el-select v-model="dataForm.asStatus" placeholder="售后状态" clearable>
                            <el-option label="全部" value=""></el-option>
                            <el-option label="未申请" value="1"></el-option>
                            <el-option label="不可申请" value="2"></el-option>
                            <el-option label="申请售后" value="3"></el-option>
                            <el-option label="审核未通过" value="4"></el-option>
                            <el-option label="审核通过" value="5"></el-option>
                        </el-select>
                </el-form-item>
                <br/>
                <el-form-item>
                    下单时间:
                    <el-date-picker
                            v-model="createTime"
                            type="daterange"
                            range-separator="-"
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
                <!--          <el-button v-if="$hasPermission('av:order:save')" type="primary" @click="addOrUpdateHandle()">{{ $t('add') }}</el-button>-->
                <!--        </el-form-item>-->
                <!--        <el-form-item>-->
                <!--          <el-button v-if="$hasPermission('av:order:delete')" type="danger" @click="deleteHandle()">{{ $t('deleteBatch') }}</el-button>-->
                <!--        </el-form-item>-->
            </el-form>
            <el-table v-loading="dataListLoading" :data="dataList" border
                      @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
<!--                <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>-->
<!--                <el-table-column prop="id" label="ID" header-align="center" align="center"></el-table-column>-->
                <el-table-column  label="订单编号" header-align="center" align="center" width="180" fixed>
                    <template slot-scope="scope">
                        <el-button type="text"  @click="showOrderDetails(scope.row.id)" class="orderNoShow">{{scope.row.orderNo}}</el-button>
                    </template>
                </el-table-column>
                <el-table-column prop="title" label="课程标题" header-align="center" align="center" width="220"></el-table-column>
                <el-table-column prop="nickname" label="用户昵称" header-align="center" align="center" width="140"></el-table-column>
<!--                <el-table-column prop="phone" label="手机号" header-align="center" align="center"></el-table-column>-->
                <el-table-column prop="type" :formatter="formatType" label="订单类型" header-align="center"
                                 align="center" width="140"></el-table-column>
                <el-table-column prop="payMoney" :formatter="cashConversion"  label="支付金额" header-align="center" align="center" width="140"></el-table-column>
                <el-table-column prop="status" :formatter="formatStatus" label="订单状态" header-align="center"
                                 align="center" width="140"></el-table-column>
                <el-table-column prop="payStatus" label="支付状态" :formatter="defrayStatus" header-align="center"
                                 align="center" width="140"></el-table-column>
                <!--        <el-table-column prop="payType" label="支付类型" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="payTime" label="支付时间" header-align="center" align="center"></el-table-column>-->
                <el-table-column prop="asStatus" label="售后状态" :formatter="customerStatus" header-align="center" align="center" width="140">
                    <template slot-scope="scope">
                        <el-tag v-if="scope.row.asStatus === 1" size="small"  type="info">未申请</el-tag>
                        <el-tag v-if="scope.row.asStatus === 2" size="small"  type="warning">不可申请</el-tag>
                        <el-tag v-if="scope.row.asStatus === 3" size="small">申请售后</el-tag>
                        <el-tag v-if="scope.row.asStatus === 4" size="small"  type="danger">审核未通过</el-tag>
                        <el-tag v-if="scope.row.asStatus === 5" size="small"  type="success">审核通过</el-tag>
                    </template>
                </el-table-column>
                <!--        <el-table-column prop="asApplyRemark" label="售后申请备注" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="asApplyExpireTime" label="售后申请失效时间（支付后10天，超过该时间后不能申请售后）" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="asApplyTime" label="售后申请时间" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="asAuthTime" label="售后审核时间" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="asAuthRemark" label="审核备注" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="playStatus" label="播放状态: 1不可播放, 2可以播放, 3已过期" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="playExpireTime" label="播放失效时间（支付后60天）" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="partnerId" label="合伙人ID" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="partnerProfit" label="合伙人收益" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="pid" label="一级ID" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="profit" label="一级收益" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="pid2" label="二级ID" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="profit2" label="二级收益" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="pid3" label="三级ID" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="profit3" label="三级收益" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="shopProfit" label="店铺收益" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="remark" label="订单备注" header-align="center" align="center"></el-table-column>-->
                <el-table-column prop="createTime" label="下单时间" header-align="center" align="center" width="180" ></el-table-column>
<!--                <el-table-column prop="asAuthTime" label="审核时间" header-align="center" align="center"></el-table-column>-->
                <el-table-column :label="$t('handle')" header-align="center" align="center" width="150" fixed="right" >
                    <template slot-scope="scope">
<!--                        <el-button v-if="$hasPermission('av:order:update') && scope.row.asStatus === 3" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">售后审核</el-button>-->
                        <el-button v-if="scope.row.type === 1" type="text" size="small"
                                   @click="showMoney(scope.row.id)">资金明细
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
<!--            <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>-->
            <!-- 弹窗, 收益明细 -->
            <order-money v-if="orderMoneyVisible" ref="orderMoney"></order-money>
            <!--点击订单编号查看订单详情-->
            <order-details v-if="orderDetailsVisible" ref="orderDetails"></order-details>
        </div>
    </el-card>
</template>
<script>
    import mixinViewModule from '@/mixins/view-module'
    import OrderMoney from './order-money'
    import AddOrUpdate from './order-add-or-update'
    import OrderDetails from './order-details'

    export default {
        mixins: [mixinViewModule],
        data() {
            return {
                orderMoneyVisible: false,
                addOrUpdateVisible: false,
                orderDetailsVisible: false,
                mixinViewModuleOptions: {
                    getDataListURL: '/av/order/page',
                    getDataListIsPage: true,
                    exportURL: '/av/order/export',
                    deleteURL: '/av/order',
                    deleteIsBatch: true
                },
                createTime: '',
                dataForm: {
                    id: '',
                    kw: '',
                    asStatus: '',
                    type: '',
                    status: '',
                    startTime: '',
                    endTime: ''

                },
                viewData: {}
            }
        },
        components: {
            OrderMoney,
            AddOrUpdate,
            OrderDetails
        },
        methods: {
            getDataList2 () {
                this.page =1
                if(this.createTime && this.createTime.length > 0){
                    this.dataForm.startTime = this.$moment(this.createTime[0]).format('YYYY-MM-DD');
                    this.dataForm.endTime = this.$moment(this.createTime[1]).format('YYYY-MM-DD');
                }
                this.getDataList()
            },

            showMoney(id) {
                this.orderMoneyVisible = true
                this.$nextTick(() => {
                    this.$refs.orderMoney.dataForm.id = id
                    this.$refs.orderMoney.init()
                })
            },

            showOrderDetails(id) {
              this.orderDetailsVisible = true
              this.$nextTick(() => {
                  this.$refs.orderDetails.dataForm.id = id
                  this.$refs.orderDetails.init()
              })
            },


            //订单类型: 1普通订单, 2赠送订单
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
            //订单状态: 1待付款, 2待发货, 3待评价, 4已完成, 5售后
            formatStatus(row) {
                switch (row.status) {
                    case 1 :
                        return '待付款'
                    case 2 :
                        return '待发货'
                    case 3 :
                        return '待评价'
                    case 4 :
                        return '已完成'
                    case 5 :
                        return '售后'
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
            //售后状态: 1未申请, 2不可申请, 3申请售后, 4审核未通过, 5审核通过
            customerStatus(row, column) {
                if (row.asStatus == null) {
                    return ''
                }
                if (row.asStatus == 1) {
                    return '未申请'
                }
                if (row.asStatus == 2) {
                    return '不可申请'
                }
                if (row.asStatus == 3) {
                    return '申请售后'
                }
                if (row.asStatus == 4) {
                    return '审核未通过'
                }
                if (row.asStatus == 5) {
                    return '审核通过'
                }
            },
            cashConversion(row,cloumn) {
                if (row.payMoney == null){
                    return 0.00
                }
                return row.payMoney.toFixed(2);
            }

        }
    }
</script>
