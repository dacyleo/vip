<template>
    <el-dialog :visible.sync="visible" width="60%" title="用户详情" :close-on-click-modal="false"
               :close-on-press-escape="false">
        <el-form :model="dataForm" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()"
                 label-width="80px">
            <el-row>
                <el-col :span="8">
                    <el-form-item label="用户头像" label-width="120px">
                        <template slot-scope="scope">
                            <img :src="dataForm.headImgUrl" width="40" height="40" class="headImgUrl"/>
                        </template>
                    </el-form-item>
                </el-col>
                <el-col :span="8">
                    <el-form-item label="用户昵称" label-width="120px">
                        {{dataForm.nickname}}
                    </el-form-item>
                </el-col>
                <el-col :span="8">
                    <el-form-item label="手机号码" label-width="120px">
                        {{dataForm.phone}}
                    </el-form-item>
                </el-col>
            </el-row>
            <el-row>
                <el-col :span="8">
                    <el-form-item label="所在省" label-width="120px">
                        {{dataForm.province}}
                    </el-form-item>
                </el-col>
                <el-col :span="8">
                    <el-form-item label="所在市" label-width="120px">
                        {{dataForm.city}}
                    </el-form-item>
                </el-col>
                <el-col :span="8">
                    <el-form-item label="注册时间" label-width="120px">
                        {{dataForm.createTime}}
                    </el-form-item>
                </el-col>
            </el-row>
            <el-row>
                <el-col :span="8">
                    <el-form-item label="累计收益" label-width="120px">
                        {{dataForm.profitTotal | formatMoney}}
                    </el-form-item>
                </el-col>
                <el-col :span="8">
                    <el-form-item label="累计推广收益" label-width="120px">
                        {{dataForm.profitSpread | formatMoney}}
                    </el-form-item>
                </el-col>
                <el-col :span="8">
                    <el-form-item label="累计合伙人收益" label-width="120px">
                        {{dataForm.profitPartner | formatMoney}}
                    </el-form-item>
                </el-col>
            </el-row>
            <el-row>
                <el-col :span="8">
                    <el-form-item label="累计订单数" label-width="120px">
                        {{dataForm.orders}}
                    </el-form-item>
                </el-col>
                <el-col :span="8">
                    <el-form-item label="累计订单金额" label-width="120px">
                        {{dataForm.orderMoneys | formatMoney}}
                    </el-form-item>
                </el-col>
                <el-col :span="8">
                    <el-form-item label="累计已提现" label-width="120px">
                        {{dataForm.profitWithdraw | formatMoney}}
                    </el-form-item>
                </el-col>
            </el-row>
            <el-row>
                <el-col :span="8">
                    <el-form-item label="累计下级订单数" label-width="120px">
                        {{dataForm.subOrders}}
                    </el-form-item>
                </el-col>
                <el-col :span="8">
                    <el-form-item label="今日新增下级数" label-width="120px">
                        {{dataForm.todaySubAdds}}
                    </el-form-item>
                </el-col>
                <el-col :span="8">
                    <el-form-item label="今日下级订单数" label-width="120px">
                        {{dataForm.todaySubOrders}}
                    </el-form-item>
                </el-col>
            </el-row>
            <el-row>
                <el-col :span="8">
                    <el-form-item label="下级成员数" label-width="120px">
                        {{dataForm.subs}}
                    </el-form-item>
                </el-col>
                <el-col :span="8">
                    <el-form-item label="认证状态" label-width="120px">
                        {{dataForm.authStatus | formatAuthStatus}}
                    </el-form-item>
                </el-col>
                <el-col :span="8">
                    <el-form-item label="认证时间" label-width="120px">
                        {{dataForm.authTime}}
                    </el-form-item>
                </el-col>
            </el-row>
            <el-row>
                <el-col :span="8">
                    <el-form-item label="可用金额" label-width="120px">
                        {{dataForm.balance | formatMoney}}
                    </el-form-item>
                </el-col>
                <el-col :span="8">
                    <el-form-item label="是否关注" label-width="120px">
                        {{dataForm.subscribe | formatSubscribeStatus}}
                    </el-form-item>
                </el-col>
                <el-col :span="8">
                    <el-form-item label="关注时间" label-width="120px">
                        {{dataForm.subscribeTime}}
                    </el-form-item>
                </el-col>
            </el-row>
            <el-row>
                <el-col :span="24">
                    <el-form-item label="备注" label-width="120px">
                        {{dataForm.remark}}
                    </el-form-item>
                </el-col>
            </el-row>
            <el-row>
                <el-col :span="12">
                    <el-form-item label="openId" label-width="120px">
                        {{dataForm.openId}}
                    </el-form-item>
                </el-col>
                <el-col :span="12">
                    <el-form-item label="用户ID" label-width="120px">
                        {{dataForm.id}}
                    </el-form-item>
                </el-col>
            </el-row>
        </el-form>
    </el-dialog>
</template>

<script>
    import debounce from 'lodash/debounce'

    export default {
        data() {
            return {
                visible: false,
                dataForm: {
                    id: '',
                    nickname: '',
                    phone: '',
                    profitTotal: '',
                    levelOneName: '',
                    levelTwoName: '',
                    levelThreeName: '',
                    headImgUrl: ''
                }
            }
        },
        filters: {
            formatAuthStatus: function (value) {
                if (!value) return '未认证'
                if (value == 1) {
                    return '未认证'
                } else if (value == 2) {
                    return '已认证'
                } else {
                    return '未认证'
                }
            },
            formatSubscribeStatus: function (value) {
                if (!value) return '未关注'
                if (value == 0) {
                    return '未关注'
                } else {
                    return '已关注'
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
                this.$http.get(`/av/user/${this.dataForm.id}`).then(({data: res}) => {
                    if (res.code !== 0) {
                        return this.$message.error(res.msg)
                    }
                    this.dataForm = {
                        ...this.dataForm,
                        ...res.data
                    }
                }).catch(() => {
                })
            }
        }
    }
</script>
