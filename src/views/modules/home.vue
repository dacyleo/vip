<template>
    <el-card :visible.sync="visible" title="按日统计" width="70%" :close-on-click-modal="false"
             :close-on-press-escape="false">
        <!--        <el-row>-->
        <!--            <el-col :span="6">-->
        <!--                <el-card shadow="always">-->
        <!--                    今日收益：{{shopData.profitDay | formatMoney}}-->
        <!--                </el-card>-->
        <!--            </el-col>-->
        <!--            <el-col :span="6">-->
        <!--                <el-card shadow="always">-->
        <!--                    累计收益：{{shopData.profitTotal | formatMoney}}-->
        <!--                </el-card>-->
        <!--            </el-col>-->
        <!--            <el-col :span="6">-->
        <!--                <el-card shadow="always">-->
        <!--                    累计订单数：{{shopData.orders}}-->
        <!--                </el-card>-->
        <!--            </el-col>-->
        <!--            <el-col :span="6">-->
        <!--                <el-card shadow="always">-->
        <!--                    累计订单金额：{{shopData.orderMoneys | formatMoney}}-->
        <!--                </el-card>-->
        <!--            </el-col>-->
        <!--        </el-row><br>-->
        <div class="item-container">
            <div class="item">
                <div class="item-title">今日收益</div>
                {{ shopData.profitDay | formatMoney }}
            </div>
            <div class="item">
                <div class="item-title">累计收益</div>
                {{ shopProfit | formatMoney }}
            </div>
            <div class="item">
                <div class="item-title">累计订单数</div>
                {{ orderAmount }}
            </div>
            <div class="item">
                <div class="item-title">累计订单金额</div>
                {{ orderMoney | formatMoney }}
            </div>
        </div>
        <el-form :inline="true" :model="dataForm" ref="dataForm"
                 :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
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
                <el-button @click="getStat()">{{ $t('query') }}</el-button>
            </el-form-item>
        </el-form>
        <!--        <el-card style="height:500px;width:100%">-->
        <div ref="echartDiv" style="height:400px;width:100%;"></div>
        <!--        </el-card>-->
    </el-card>
</template>

<script>
    export default {
        data() {
            return {
                orderMoney: '',
                shopProfit: '',
                orderAmount: '',
                shopData: {},
                visible: false,
                tempDate: '',
                dataForm: {
                    startTime: '',
                    endTime: ''
                }
            }
        },
        mounted() {
            this.init()
        },
        filters: {
            formatMoney: function (value) {
                if (!value) return '0.00'
                return value.toFixed(2)
            }
        },
        methods: {
            init() {
                this.visible = true
                //默认查询最近七天
                const date = new Date()
                const date2 = new Date().setTime(date.getTime() - 3600 * 1000 * 24 * 7)
                this.tempDate = []
                this.tempDate.push(date2)
                this.tempDate.push(date)
                this.getStat()
            },
            getStat() {
                if (!this.tempDate || this.tempDate === '' || this.tempDate.length === 0) {
                    this.$message.warning('开始时间和结束时间不能为空')
                    return
                }
                var that = this
                this.$http.get('/av/report/homeStat?startTime=' + this.$moment(this.tempDate[0]).format('YYYY-MM-DD') + '&endTime=' + this.$moment(this.tempDate[1]).format('YYYY-MM-DD')).then(({data: res}) => {
                    if (res.code !== 0) {
                        that.$message.error(res.msg)
                    } else {
                        this.shopData = res.shopDTO
                        this.orderMoney = res.orderMoney
                        this.shopProfit = res.shopProfit
                        this.orderAmount = res.orderAmount
                        var dayData = res.days   //统计日期
                        var visitData = []  //每日访客
                        res.visits.forEach(item => {
                            visitData.push(item.visits)
                        })
                        var shopData = []   //每日收益
                        res.shopMoneys.forEach(item => {
                            shopData.push(item.shopMoneys)
                        })
                        var payMoneyData = [] //每日流水
                        res.payMoneys.forEach(item => {
                            payMoneyData.push(item.payMoneys)
                        })
                        var shareData = []  //每日分享
                        res.shares.forEach(item => {
                            shareData.push(item.shares)
                        })
                        var payOrderData = []  //每日订单数
                        res.payOrders.forEach(item => {
                            payOrderData.push(item.payOrders)
                        })
                        var option = {
                            title: {
                                text: ''
                            },
                            tooltip: {
                                trigger: 'axis'
                            },
                            legend: {
                                data: ['访客数', '店铺收益', '付款金额', '分享次数', '订单数']
                            },
                            grid: {
                                left: '3%',
                                right: '4%',
                                bottom: '3%',
                                containLabel: true
                            },
                            toolbox: {
                                feature: {
                                    saveAsImage: {}
                                }
                            },
                            xAxis: {
                                type: 'category',
                                boundaryGap: false,
                                data: dayData
                            },
                            yAxis: {
                                type: 'value'
                            },
                            series: [{
                                name: '访客数',
                                type: 'line',
                                // stack: '总量',
                                data: visitData,
                                smooth: true
                            }, {
                                name: '店铺收益',
                                type: 'line',
                                // stack: '总量',
                                data: shopData,
                                smooth: true
                            }, {
                                name: '付款金额',
                                type: 'line',
                                // stack: '总量',
                                data: payMoneyData,
                                smooth: true
                            }, {
                                name: '分享次数',
                                type: 'line',
                                // stack: '总量',
                                data: shareData,
                                smooth: true
                            }, {
                                name: '订单数',
                                type: 'line',
                                // stack: '总量',
                                data: payOrderData,
                                smooth: true
                            }]
                        }
                        var myChart = this.$echarts.init(that.$refs.echartDiv)
                        myChart.setOption(option)
                    }
                }).catch(() => {
                })
            }
        }
    }
</script>

<style scoped lang="scss">

    .item-container {
        width: 100%;
        display: flex;
        flex-direction: row;
        /*justify-content: space-around;*/
        justify-content: space-between;
        margin-bottom: 40px;

    .item {
        width: 200px;
        height: 120px;
        display: flow;
        box-shadow: 0 2px 12px 0 rgba(0, 0, 0, .1);
        font-size: 20px;
        line-height: 90px;
        text-align: center;
        border-radius: 4px;
        font-weight: bolder;

    .item-title {
        border-radius: 4px 4px 0px 0px;
        width: 100%;
        height: 30px;
        line-height: 30px;
        font-size: 14px;
        font-weight: normal;
        background-color: #17B3A3;
        color: #f0f0f0;
    }

    }

    }

</style>
