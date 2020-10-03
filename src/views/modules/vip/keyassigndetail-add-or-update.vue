<template>
    <el-dialog :visible.sync="visible" title="发送激活码" :close-on-click-modal="false" :close-on-press-escape="false">
        <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()"
                 :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
            <el-form-item label="手机号" prop="phoneStr">
                <el-input v-model="dataForm.phoneStr" type="textarea" :rows="18" placeholder="手机号,多个用英文逗号分隔"></el-input>
            </el-form-item>
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
                visible: false,
                dataForm: {
                    phoneStr: ''
                }
            }
        },
        computed: {
            dataRule() {
                return {
                    phoneStr: [
                        {required: true, message: '手机号不能为空', trigger: 'blur'}
                    ]
                }
            }
        },
        methods: {
            init() {
                this.visible = true
            },
            // 表单提交
            dataFormSubmitHandle: debounce(function () {
                this.$refs['dataForm'].validate((valid) => {
                    if (!valid) {
                        return false
                    }
                    this.$http['post']('/vip/keyassigndetail/send-key?phoneStr='+this.dataForm.phoneStr).then(({data: res}) => {
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
                })
            }, 1000, {'leading': true, 'trailing': false})
        }
    }
</script>
