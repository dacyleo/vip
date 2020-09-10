<template>
    <el-dialog :visible.sync="visible" title="按日成交统计" width="70%" :close-on-click-modal="false"
               :close-on-press-escape="false">
        <el-form :inline="true" :model="dataForm" ref="dataForm" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
            <el-form-item label="成交时间">
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
    </el-dialog>
</template>

<script>
    export default {
        data() {
            return {
                visible: false,
                tempDate: '',
                dataForm: {
                    id: '',
                    startTime: '',
                    endTime: ''
                }
            }
        },
        methods: {
            init() {
                this.visible = true
                //默认查询最近七天
                const date = new Date();
                const date2 = new Date().setTime(date.getTime() - 3600 * 1000 * 24 * 7)
                this.tempDate = []
                this.tempDate.push(date2)
                this.tempDate.push(date)
                this.getStat()
            },
            getStat() {
                if (!this.tempDate || this.tempDate === '' || this.tempDate.length === 0) {
                    this.$message.warning("开始时间和结束时间不能为空")
                    return
                }
                var that = this
                this.$http.get('/av/report/courseDayMoneyStat?courseId=' + this.dataForm.id + '&startTime=' + this.$moment(this.tempDate[0]).format("YYYY-MM-DD") + '&endTime=' + this.$moment(this.tempDate[1]).format("YYYY-MM-DD")).then(({data: res}) => {
                    if (res.code !== 0) {
                        that.$message.error(res.msg)
                    } else {
                        var xData = []
                        var yData = []
                        res.list.forEach(item => {
                            xData.push(item.day)
                            yData.push(item.dealMoney)
                        });
                        var option = {
                            title: {
                                // text: res.courseTitle
                            },
                            tooltip: {},
                            legend: {
                                data: ['交易额']
                            },
                            xAxis: {
                                type: 'category',
                                name: '日期',
                                data: xData
                            },
                            yAxis: {
                                type: 'value',
                                name: '交易额'
                            },
                            series: [{
                                data: yData,
                                type: 'line',
                                smooth: true
                            }]
                        };
                        var myChart = this.$echarts.init(that.$refs.echartDiv)
                        myChart.setOption(option);
                    }
                }).catch(() => {
                })
            }
        }
    }
</script>
