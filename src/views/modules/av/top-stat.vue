<template>
    <el-card :visible.sync="visible" width="70%" :close-on-click-modal="false"
               :close-on-press-escape="false">
        <el-form :inline="true" :model="dataForm" ref="dataForm" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
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
        <el-row>
            <el-col :span="12">
                <div ref="echartDivCourse" style="height:400px;width:100%;"></div>
            </el-col>
            <el-col :span="12">
                <div ref="echartDivProxy" style="height:400px;width:100%;"></div>
            </el-col>
        </el-row>
    </el-card>
</template>

<script>
    export default {
        data() {
            return {
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
                this.$http.get('/av/report/topStat?startTime=' + this.$moment(this.tempDate[0]).format("YYYY-MM-DD") + '&endTime=' + this.$moment(this.tempDate[1]).format("YYYY-MM-DD")).then(({data: res}) => {
                    if (res.code !== 0) {
                        that.$message.error(res.msg)
                    } else {
                        var topCourseDataX = []   //课程收益排行
                        var topCourseDataY = []   //课程收益排行
                        res.topCourse.forEach(item => {
                            topCourseDataX.push(item.name)
                            topCourseDataY.push(item.money)
                        })
                        var topProxyDataX = []   //推广员收益排行
                        var topProxyDataY = []   //推广员收益排行
                        res.topProxy.forEach(item => {
                            topProxyDataX.push(item.name)
                            topProxyDataY.push(item.money)
                        })
                        var optionCourse = {
                            title: {
                                // text: res.courseTitle
                            },
                            tooltip: {},
                            legend: {
                                data: ['收益金额']
                            },
                            xAxis: {
                                type: 'category',
                                name: '标题',
                                data: topCourseDataX
                            },
                            yAxis: {
                                type: 'value',
                                name: '收益金额'
                            },
                            series: [{
                                data: topCourseDataY,
                                type: 'line',
                                smooth: true
                            }]
                        }
                        var optionProxy = {
                            title: {
                                // text: res.courseTitle
                            },
                            tooltip: {},
                            legend: {
                                data: ['收益金额']
                            },
                            xAxis: {
                                type: 'category',
                                name: '推广员',
                                data: topProxyDataX
                            },
                            yAxis: {
                                type: 'value',
                                name: '收益金额'
                            },
                            series: [{
                                data: topProxyDataY,
                                type: 'line',
                                smooth: true
                            }]
                        }
                        var courseChart = this.$echarts.init(that.$refs.echartDivCourse)
                        var proxyChart = this.$echarts.init(that.$refs.echartDivProxy)
                        courseChart.setOption(optionCourse)
                        proxyChart.setOption(optionProxy)
                    }
                }).catch(() => {
                })
            }
        }
    }
</script>
