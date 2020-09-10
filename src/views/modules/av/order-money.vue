<template>
    <el-dialog :visible.sync="visible" title="资金明细" :close-on-click-modal="false"
               :close-on-press-escape="false">
        <el-form label-position="left" :model="dataForm" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()"
                 :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
            <el-form-item label="课程标题" prop="orderNo">
                {{dataForm.courseName}}
            </el-form-item>
            <el-form-item label="订单编号" prop="orderNo">
                {{dataForm.orderNo}}
            </el-form-item>

            <el-form-item label="支付金额" prop="payMoney">
                {{dataForm.payMoney | formatMoney}}
            </el-form-item>
            <el-table :data="tableData">
                <el-table-column
                        prop="name"
                        label="角色"
                        >
                </el-table-column>
                <el-table-column
                        prop="profit"
                        label="收益"
                        >
                </el-table-column>>
            </el-table>
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
                    obj2.name = '合伙人->'+this.dataForm.partnerName
                    obj2.profit = this.dataForm.partnerProfit.toFixed(2)
                    var obj3 = {}
                    obj3.name = '一级分销员->'+this.dataForm.puserName
                    obj3.profit = this.dataForm.profit.toFixed(2)
                    var obj4 = {}
                    obj4.name = '二级分销员->'+this.dataForm.p2UserName
                    obj4.profit = this.dataForm.profit2.toFixed(2)
                    var obj5 = {}
                    obj5.name = '三级分销员->'+this.dataForm.p3UserName
                    obj5.profit = this.dataForm.profit3.toFixed(2)
                    if(obj.profit > 0){
                        this.tableData.push(obj)
                    }
                    if(obj2.profit > 0){
                        this.tableData.push(obj2)
                    }
                    if(obj3.profit > 0){
                        this.tableData.push(obj3)
                    }
                    if(obj4.profit > 0){
                        this.tableData.push(obj4)
                    }
                    if(obj5.profit > 0){
                        this.tableData.push(obj5)
                    }
                }).catch(() => {
                })
            },

        }
    }
</script>
