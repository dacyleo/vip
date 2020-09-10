<template>
    <el-dialog :visible.sync="visible" title="新增工资" :close-on-click-modal="false" :close-on-press-escape="false">
        <el-form :model="dataForm" ref="dataForm" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
            <el-form-item label="用户昵称" prop="uidName">
                <el-input v-model="dataForm.uidName" readonly placeholder="用户昵称" style="width:150px;"></el-input>
                &nbsp;<el-button type="primary" icon="el-icon-search" @click="chooseUser()">选择</el-button>
            </el-form-item>
            <el-form-item label="工资金额" prop="money">
                <el-input v-model="dataForm.money" style="width:150px" placeholder="金额"></el-input>
            </el-form-item>
            <el-form-item label="备注" prop="remark">
                <el-input v-model="dataForm.remark" type="textarea" style="width:300px" placeholder="备注"></el-input>
            </el-form-item>
        </el-form>
        <template slot="footer">
            <el-button @click="visible = false">{{ $t('cancel') }}</el-button>
            <el-button type="primary" @click="salaryAdd()">{{ $t('confirm') }}</el-button>
        </template>
        <choose-salary-user v-if="chooseSalaryUserVisible" ref="chooseSalaryUser" @getUser="confirmChooseUser"></choose-salary-user>
    </el-dialog>
</template>

<script>
    import chooseSalaryUser from './choose-salary-user'

    export default {
        data() {
            return {
                visible: false,
                chooseSalaryUserVisible: false,
                dataForm: {
                    id: '',
                    uid: '',
                    uidName: '',
                    type: '',
                    status: '',
                    orderId: '',
                    money: '',
                    moneyStatus: '',
                    content: '',
                    remark: '',
                    createTime: ''
                }
            }
        },
        components: {chooseSalaryUser},
        methods: {
            init: function () {
                this.visible = true
                this.$refs['dataForm'].resetFields()
            },
            chooseUser: function () {
                this.chooseSalaryUserVisible = true
                this.$nextTick(() => {
                    this.$refs.chooseSalaryUser.init()
                })
            },
            confirmChooseUser: function (data) {
                this.dataForm.uid = data.id
                this.dataForm.uidName = data.nickname
            },
            salaryAdd: function () {
                var that = this
                this.$http.post(`/av/fund/salaryAdd`, {
                    "uid": that.dataForm.uid,
                    "money": that.dataForm.money,
                    "remark": that.dataForm.remark
                }).then(({data: res}) => {
                    if (res.code !== 0) {
                        return that.$message.error(res.msg)
                    } else {
                        that.$emit('refreshList')
                        that.visible = false
                    }
                }).catch(() => {
                })
            }
        }
    }
</script>
