<template>
    <el-dialog :visible.sync="visible" title="订单详情" :close-on-click-modal="false"
               :close-on-press-escape="false">
        <el-form label-position="left" :model="dataForm" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()"
                 :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
            <el-row>
                <el-col :span="12">
                    <el-form-item label="订单编号" prop="orderNo">
                        {{dataForm.orderNo}}
                    </el-form-item>
                </el-col>
                <el-col :span="12">
                    <el-form-item label="订单价格" prop="payMoney">
                        {{dataForm.payMoney | formatMoney}}
                    </el-form-item>
                </el-col>
            </el-row>
            <el-row>
                <el-col :span="12">
                    <el-form-item label="买家昵称">
                        {{dataForm.nickname}}
                    </el-form-item>
                </el-col>
                <el-col :span="12">
                    <el-form-item label="电话号码">
                        {{dataForm.phone}}
                    </el-form-item>
                </el-col>
            </el-row>
            <el-row>
                <el-col :span="12">
                    <el-form-item label="下单时间">
                        {{dataForm.createTime}}
                    </el-form-item>
                </el-col>
                <el-col :span="12">
                    <el-form-item label="支付类型">
                        {{dataForm.payType}}
                    </el-form-item>
                </el-col>
            </el-row>

            <el-row>
                <el-col :span="12">
                    <el-form-item label="订单类型">
                        {{dataForm.type}}
                    </el-form-item>
                </el-col>
                <el-col :span="12">
                    <el-form-item label="支付时间">
                        {{dataForm.payTime}}
                    </el-form-item>
                </el-col>
            </el-row>
            <el-row>
                <el-col :span="12">
                    <el-form-item label="订单状态">
                        {{dataForm.status}}
                    </el-form-item>
                </el-col>
            </el-row>
        </el-form>
    </el-dialog>
</template>

<script>

    export default {
        data() {
            return {
                tableData: [],
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
                    asStatus: '',
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
                    createTime: '',
                    partnerName: '',
                    puserName: '',
                    p2UserName: '',
                    p3UserName: ''
                }
            }
        },
        methods: {
            getPayType() {
                if (this.dataForm.payType == 1) {
                    this.dataForm.payType = '线下支付'
                }
                if (this.dataForm.payType == 2) {
                    this.dataForm.payType = '微信支付'
                }
                if (this.dataForm.payType == 3) {
                    this.dataForm.payType = '小程序支付'
                }
            },

            getOrderType() {
                if (this.dataForm.type == 1) {
                    this.dataForm.type = '普通订单'
                }
                if (this.dataForm.type == 2) {
                    this.dataForm.type = '赠送订单'
                }
            },

            getOrderStatus(){
                if (this.dataForm.status == 1) {
                    this.dataForm.status = '待付款'
                }
                if (this.dataForm.status == 2) {
                    this.dataForm.status = '待发货'
                }
                if (this.dataForm.status == 3) {
                    this.dataForm.status = '待评价'
                }
                if (this.dataForm.status == 4) {
                    this.dataForm.status = '已完成'
                }
                if (this.dataForm.status == 5) {
                    this.dataForm.status = '售后'
                }
            },

            init() {
                this.visible = true
                this.$nextTick(() => {
                    if (this.dataForm.id) {
                        this.getInfo()
                    }
                })
            },
            // 获取信息
            getInfo() {
                this.$http.get(`/av/order/${this.dataForm.id}`).then(({data: res}) => {
                    if (res.code !== 0) {
                        return this.$message.error(res.msg)
                    }
                    this.dataForm = {
                        ...this.dataForm,
                        ...res.data
                    }
                    //初始化tableData
                    var obj = {}
                    this.tableData = []
                    obj.name = '店铺'
                    obj.profit = this.dataForm.shopProfit.toFixed(2)
                    var obj2 = {}
                    obj2.name = '合伙人->' + this.dataForm.partnerName
                    obj2.profit = this.dataForm.partnerProfit.toFixed(2)
                    var obj3 = {}
                    obj3.name = '一级分销员->' + this.dataForm.puserName
                    obj3.profit = this.dataForm.profit.toFixed(2)
                    var obj4 = {}
                    obj4.name = '二级分销员->' + this.dataForm.p2UserName
                    obj4.profit = this.dataForm.profit2.toFixed(2)
                    var obj5 = {}
                    obj5.name = '三级分销员->' + this.dataForm.p3UserName
                    obj5.profit = this.dataForm.profit3.toFixed(2)
                    if (obj.profit > 0) {
                        this.tableData.push(obj)
                    }
                    if (obj2.profit > 0) {
                        this.tableData.push(obj2)
                    }
                    if (obj3.profit > 0) {
                        this.tableData.push(obj3)
                    }
                    if (obj4.profit > 0) {
                        this.tableData.push(obj4)
                    }
                    if (obj5.profit > 0) {
                        this.tableData.push(obj5)
                    }
                    this.getPayType();
                    this.getOrderType();
                    this.getOrderStatus();
                }).catch(() => {
                })
            }

        },

    }
</script>
