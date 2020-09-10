<template>
    <el-dialog :visible.sync="visible" title="查看上级" :close-on-click-modal="false"
               :close-on-press-escape="false">
        <el-form :model="dataForm"  ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()"
                 :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
            <!--            </el-form-item>-->
            <el-form-item label="用户昵称:" prop="nickname">
                {{dataForm.nickname}}
            </el-form-item>
            <el-form-item label="手机号码:" prop="phone">
                {{dataForm.phone}}
            </el-form-item>
            <el-form-item label="用户头像" prop="headImgUrl">
                <template slot-scope="scope">
                    <img :src="dataForm.headImgUrl" width="40" height="40" class="headImgUrl"/>
                </template>
            </el-form-item>
            <el-form-item label="一级推广:" prop="levelOneName">
                {{dataForm.levelOneName}}
            </el-form-item>
            <el-form-item label="二级推广:" prop="levelTwoName">
                {{dataForm.levelTwoName}}
            </el-form-item>
            <el-form-item label="三级推广:" prop="levelThreeName">
                {{dataForm.levelThreeName}}
            </el-form-item>
            <el-form-item label="累计收益:" prop="profitTotal">
                {{dataForm.profitTotal | formatMoney}}
            </el-form-item>
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
                this.$http.get(`/av/user/findUserSuperiorByUid/${this.dataForm.id}`).then(({data: res}) => {
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
