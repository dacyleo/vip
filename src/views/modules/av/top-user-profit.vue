<template>
    <el-card shadow="never" class="aui-card--fill">
        <div class="mod-av__user}">
            <el-form :inline="true" :model="dataForm">
                <el-form-item label="统计时间">
                    <el-date-picker
                            v-model="tempDate"
                            type="daterange"
                            range-separator="至"
                            start-placeholder="开始日期"
                            end-placeholder="结束日期">
                    </el-date-picker>
                </el-form-item>
                <el-form-item>
                    <el-button @click="getDataList2()">{{ $t('query') }}</el-button>
                </el-form-item>
            </el-form>
            <el-table :data="dataList" border
                      style="width: 100%;"
            >
                <el-table-column prop="nickname" label="昵称" header-align="center" align="center"
                                 fixed></el-table-column>
                <el-table-column label="头像" header-align="center" align="center">
                    <template slot-scope="scope">
                        <img :src="scope.row.headImgUrl" width="40" height="40" class="head_pic"/>
                    </template>
                </el-table-column>
                <el-table-column prop="phone" label="手机号" header-align="center" align="center"
                ></el-table-column>
                <el-table-column prop="profitMoney" :formatter="formatMoney" label="总收益金额" header-align="center" align="center"
                                 ></el-table-column>
                <el-table-column prop="remark" label="备注" header-align="center" align="center"
                ></el-table-column>
                <el-table-column prop="createTime" label="注册时间" header-align="center" align="center"
                ></el-table-column>
            </el-table>
        </div>
    </el-card>
</template>
<script>
    import Cookies from 'js-cookie'

    export default {
        data() {
            return {
                dataList: [],
                tempDate: '',
                dataList: [],
                dataForm: {
                    startTime: '',
                    endTime: ''
                }
            }
        },
        mounted() {
            this.init()
        },
        methods: {
            getDataList2(){
                this.page = 1
                this.getDataList()
            },
            formatMoney: function (item) {
                if (!item.profitMoney) return '0.00'
                return item.profitMoney.toFixed(2)
            },
            init: function () {
                //默认查询最近七天
                const date = new Date();
                const date2 = new Date().setTime(date.getTime() - 3600 * 1000 * 24 * 7)
                this.tempDate = []
                this.tempDate.push(date2)
                this.tempDate.push(date)
                this.getDataList()
            },
            getDataList: function () {
                if (!this.tempDate || this.tempDate === '' || this.tempDate.length === 0) {
                    this.$message.warning("开始时间和结束时间不能为空")
                    return
                }
                var that = this
                this.$http.get('/av/report/topProxyStat?startTime=' + this.$moment(this.tempDate[0]).format("YYYY-MM-DD") + '&endTime=' + this.$moment(this.tempDate[1]).format("YYYY-MM-DD")).then(({data: res}) => {
                    if (res.code !== 0) {
                        that.$message.error(res.msg)
                    } else {
                        that.dataList = res.topProxyStat
                    }
                }).catch(() => {
                })
            }
        }
    }
</script>
