<template>
    <el-dialog :visible.sync="visible" title="售后审核" :close-on-click-modal="false" :close-on-press-escape="false">
        <el-form :model="dataForm" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()"
                 :label-width="'150px'">
            <el-row :gutter="20">
                <el-col :span="8">
                    <el-form-item label="订单编号" prop="orderNo">
                        <!--                    <el-input v-model="dataForm.orderNo" placeholder="订单编号" readonly="true"></el-input>-->
                        {{dataForm.orderNo}}
                    </el-form-item>

                </el-col>
                <!--            <el-col :span="8">-->
                <!--                <el-form-item label="用户昵称" prop="uid">-->
                <!--                  <el-input v-model="dataForm.uid" placeholder="用户ID"></el-input>-->
                <!--              </el-form-item>-->
                <!--            </el-col>-->
                <!--            <el-col :span="8">-->
                <!--                <el-form-item label="课程标题" prop="courseId">-->
                <!--                    <el-input v-model="dataForm.courseId" placeholder="课程ID"></el-input>-->
                <!--                </el-form-item>-->
                <!--            </el-col>-->
                <!--            <el-col :span="12">-->
                <!--                <el-form-item label="订单类型" prop="type">-->
                <!--                    <el-input v-model="dataForm.type" placeholder="订单类型"></el-input>-->
                <!--                </el-form-item>-->
                <!--            </el-col>-->
                <!--            <el-col :span="12">-->
                <!--                <el-form-item label="订单状态" prop="status">-->
                <!--                    <el-input v-model="dataForm.status" placeholder="订单状态"></el-input>-->
                <!--                </el-form-item>-->
                <!--            </el-col>-->
                <!--            <el-col :span="6">-->
                <!--                <el-form-item label="支付金额" prop="payMoney">-->
                <!--                    <el-input v-model="dataForm.payMoney" placeholder="支付金额"></el-input>-->
                <!--                </el-form-item>-->
                <!--            </el-col>-->
                <!--            <el-col :span="8">-->
                <!--                <el-form-item label="支付状态" prop="payStatus">-->
                <!--                    <el-input v-model="dataForm.payStatus" placeholder="支付状态"></el-input>-->
                <!--                </el-form-item>-->
                <!--            </el-col>-->
                <!--            <el-col :span="8">-->
                <!--                <el-form-item label="支付类型" prop="payType">-->
                <!--                    <el-input v-model="dataForm.payType" placeholder="支付类型"></el-input>-->
                <!--                </el-form-item>-->
                <!--            </el-col>-->
                <!--            <el-col :span="12">-->
                <!--                <el-form-item label="支付时间" prop="payTime">-->
                <!--                    <el-input v-model="dataForm.payTime" placeholder="支付时间"></el-input>-->
                <!--                </el-form-item>-->
                <!--            </el-col>-->
                <el-col :span="24">
                    <el-form-item label="申请原因" prop="asApplyRemark">
                        <!--                    <el-input type="textarea" rows="3" v-model="dataForm.asApplyRemark" placeholder="售后申请备注" readonly="true"></el-input>-->
                        {{dataForm.asApplyRemark}}
                    </el-form-item>
                </el-col>
                <!--            <el-col :span="12">-->
                <!--                <el-form-item label="申请时间" prop="asApplyTime">-->
                <!--                    <el-input v-model="dataForm.asApplyTime" placeholder="售后申请时间"></el-input>-->
                <!--                </el-form-item>-->
                <!--            </el-col>-->
                <!--            <el-col :span="12">-->
                <!--                <el-form-item label="审核时间" prop="asAuthTime">-->
                <!--                    <el-input v-model="dataForm.asAuthTime" placeholder="售后审核时间"></el-input>-->
                <!--                </el-form-item>-->
                <!--            </el-col>-->
                <!--            <el-col :span="24">-->
                <!--                <el-form-item label="失效时间" prop="asApplyExpireTime">-->
                <!--                    <el-input v-model="dataForm.asApplyExpireTime" placeholder="售后申请失效时间（支付后10天，超过该时间后不能申请售后）"></el-input>-->
                <!--                </el-form-item>-->
                <!--            </el-col>-->
                <el-col :span="24">
                    <el-form-item label="审核备注" prop="asAuthRemark">
                        <el-input type="textarea" v-model="dataForm.asAuthRemark" placeholder="审核备注"
                                  rows="3"></el-input>
                    </el-form-item>
                </el-col>

                <el-col :span="12">
                    <el-form-item label="售后状态" prop="asStatus">
                        <el-select v-model="dataForm.asStatus" placeholder="售后状态">
                            <el-option label="审核不通过" :value="4"></el-option>
                            <el-option label="审核通过" :value="5"></el-option>
                        </el-select>
                        <!--                    <el-input v-model="dataForm.asStatus" placeholder="售后状态"></el-input>-->
                    </el-form-item>
                </el-col>
                <el-col :span="24">
                    <el-form-item label="退款资金来源">
                        <el-radio v-model="refundAccount" label="UNSETTLED_FUNDS">未结算资金</el-radio>
                        <el-radio v-model="refundAccount" label="RECHARGE_FUNDS">账户余额</el-radio>
                    </el-form-item>
                </el-col>
            </el-row>

            <!--          <el-form-item label="播放状态" prop="playStatus">-->
            <!--          <el-input v-model="dataForm.playStatus" placeholder="播放状态"></el-input>-->
            <!--      </el-form-item>-->
            <!--          <el-form-item label="播放失效时间（支付后60天）" prop="playExpireTime">-->
            <!--          <el-input v-model="dataForm.playExpireTime" placeholder="播放失效时间（支付后60天）"></el-input>-->
            <!--      </el-form-item>-->
            <!--          <el-form-item label="合伙人ID" prop="partnerId">-->
            <!--          <el-input v-model="dataForm.partnerId" placeholder="合伙人ID"></el-input>-->
            <!--      </el-form-item>-->
            <!--          <el-form-item label="合伙人收益" prop="partnerProfit">-->
            <!--          <el-input v-model="dataForm.partnerProfit" placeholder="合伙人收益"></el-input>-->
            <!--      </el-form-item>-->
            <!--          <el-form-item label="一级ID" prop="pid">-->
            <!--          <el-input v-model="dataForm.pid" placeholder="一级ID"></el-input>-->
            <!--      </el-form-item>-->
            <!--          <el-form-item label="一级收益" prop="profit">-->
            <!--          <el-input v-model="dataForm.profit" placeholder="一级收益"></el-input>-->
            <!--      </el-form-item>-->
            <!--          <el-form-item label="二级ID" prop="pid2">-->
            <!--          <el-input v-model="dataForm.pid2" placeholder="二级ID"></el-input>-->
            <!--      </el-form-item>-->
            <!--          <el-form-item label="二级收益" prop="profit2">-->
            <!--          <el-input v-model="dataForm.profit2" placeholder="二级收益"></el-input>-->
            <!--      </el-form-item>-->
            <!--          <el-form-item label="三级ID" prop="pid3">-->
            <!--          <el-input v-model="dataForm.pid3" placeholder="三级ID"></el-input>-->
            <!--      </el-form-item>-->
            <!--          <el-form-item label="三级收益" prop="profit3">-->
            <!--          <el-input v-model="dataForm.profit3" placeholder="三级收益"></el-input>-->
            <!--      </el-form-item>-->
            <!--          <el-form-item label="店铺收益" prop="shopProfit">-->
            <!--          <el-input v-model="dataForm.shopProfit" placeholder="店铺收益"></el-input>-->
            <!--      </el-form-item>-->
            <!--          <el-form-item label="订单备注" prop="remark">-->
            <!--          <el-input v-model="dataForm.remark" placeholder="订单备注"></el-input>-->
            <!--      </el-form-item>-->
            <!--          <el-form-item label="创建时间" prop="createTime">-->
            <!--          <el-input v-model="dataForm.createTime" placeholder="创建时间"></el-input>-->
            <!--      </el-form-item>-->
        </el-form>
        <template slot="footer">
            <el-button @click="visible = false">{{ $t('cancel') }}</el-button>
            <el-button type="primary" @click="dataFormSubmitHandle()">{{ $t('confirm') }}</el-button>
        </template>
    </el-dialog>
</template>

<script>
    import debounce from 'lodash/debounce'

    export default {
        data() {
            return {
                refundAccount: 'UNSETTLED_FUNDS',
                visible: false,
                dataForm: {
                    id: '',
                    uid: '',
                    courseId: '',
                    orderNo: '',
                    type: '',
                    status: '',
                    payMoney: '',
                    payStatus: '',
                    payType: '',
                    payTime: '',
                    asStatus: 5,
                    asApplyRemark: '',
                    asApplyExpireTime: '',
                    asApplyTime: '',
                    asAuthTime: '',
                    asAuthRemark: '',
                    playStatus: '',
                    playExpireTime: '',
                    partnerId: '',
                    partnerProfit: '',
                    pid: '',
                    profit: '',
                    pid2: '',
                    profit2: '',
                    pid3: '',
                    profit3: '',
                    shopProfit: '',
                    remark: '',
                    createTime: ''
                }
            }
        },
        methods: {
            init() {
                this.visible = true
                this.$nextTick(() => {
                    this.$refs['dataForm'].resetFields()
                    if (this.dataForm.id) {
                        this.getInfo()
                    }
                })
            },
            // 获取信息
            getInfo() {
                this.$http.get(`/av/order/getNew?id=${this.dataForm.id}`).then(({data: res}) => {
                    if (res.code !== 0) {
                        return this.$message.error(res.msg)
                    }
                    if (res.data.asStatus !== 5) {
                        res.data.asStatus = 5
                    }
                    this.dataForm = {
                        ...this.dataForm,
                        ...res.data
                    }
                }).catch(() => {
                })
            },
            // 表单提交
            dataFormSubmitHandle: debounce(function () {
                var formData = {
                    "id": this.dataForm.id,
                    "asStatus": this.dataForm.asStatus,
                    "asAuthRemark": this.dataForm.asAuthRemark,
                    "refundAccount": this.refundAccount
                }
                this.$http.post('/av/order/orderRefundAudit', formData).then(({data: res}) => {
                    if (res.code !== 0) {
                        return this.$message.error(res.msg)
                    }
                    this.$message({
                        message: this.$t('prompt.success'),
                        type: 'success',
                        duration: 500,
                        onClose: () => {
                            this.visible = false
                            this.$emit('refreshDataList')
                        }
                    })
                }).catch(() => {
                })
            }, 1000, {'leading': true, 'trailing': false})
        }
    }
</script>
