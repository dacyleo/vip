<template>
    <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false"
               :close-on-press-escape="false">
        <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()"
                 :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
            <el-form-item prop="type" label="商户类型" size="mini">
                <el-radio-group v-model="dataForm.type">
                    <el-radio label="0">普通商户</el-radio>
                    <el-radio label="1">0元换购商户</el-radio>
                </el-radio-group>
            </el-form-item>
            <el-form-item label="商户名称" prop="name">
                <el-input v-model="dataForm.name" placeholder="商户名称"></el-input>
            </el-form-item>
            <el-form-item label="商户图片" prop="picUrl">
                <el-upload
                        class="avatar-uploader"
                        action=""
                        :http-request="uploadFileElment"
                        :show-file-list="false"
                        :on-success="handleSuccess"
                        :before-upload="beforeUpload">
                    <img style="width: 94px;height: 94px;"
                         v-if="dataForm.picUrl" :src="dataForm.picUrl">
                    <img style="width: 94px;height: 94px;" v-else
                         src="../../../assets/img/update.png">
                </el-upload>
            </el-form-item>
            <el-form-item label="商户地址" prop="address">
                <el-input v-model="dataForm.address" placeholder="请填写商户地址"></el-input>
            </el-form-item>
            <el-form-item label="商户简介" prop="content">
                <el-input v-model="dataForm.content" placeholder="请填写如下格式：['简介第一部分','简介第二部分','简介第三部分']"></el-input>
            </el-form-item>
            <el-form-item label="核销员手机号" prop="phone">
                <el-input v-model="dataForm.phone" placeholder="请填写核销员手机号"></el-input>
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
    import Cookies from 'js-cookie'

    export default {
        data() {
            return {
                visible: false,
                dataForm: {
                    id: '',
                    type: '0',
                    name: '',
                    picUrl: '',
                    content: '',
                    sysUserId: '',
                    createTime: '',
                    phone: '',
                    address: ''
                }
            }
        },
        computed: {
            dataRule() {
                return {
                    type: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ],
                    name: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ],
                    picUrl: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ],
                    content: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ],
                    address: [
                        {required: true, message: '商户地址不能为空', trigger: 'blur'}
                    ],
                    sysUserId: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ],
                    createTime: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ],
                    phone: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ]
                }
            }
        },
        methods: {
            init() {
                this.visible = true
                this.$nextTick(() => {
                    this.$refs['dataForm'].resetFields()
                    if (this.dataForm.id) {
                        this.getInfo()
                    }
                })
            },
            // 获取信息
            getInfo() {
                this.$http.get(`/vip/shop/${this.dataForm.id}`).then(({data: res}) => {
                    if (res.code !== 0) {
                        return this.$message.error(res.msg)
                    }
                    this.dataForm = {
                        ...this.dataForm,
                        ...res.data
                    }
                    this.dataForm.type = res.data.type.toString()
                }).catch(() => {
                })
            },
            handleSuccess: function (res, file) {
                console.log(res);
                this.dataForm.picUrl = res.data.data.src
            },
            beforeUpload: function (file) {
                var isJPG = (file.type === 'image/jpeg' || file.type === 'image/jpg' || file.type === 'image/png')
                var isLt2M = file.size / 1024 / 1024 < 2

                if (!isJPG) {
                    this.$message.error("上传图片格式只能是JPG/JPEG/PNG/三种格式！")
                }
                if (!isLt2M) {
                    this.$message.error("上传图片大小不能超过 2MB!")
                }
                return isJPG && isLt2M
            },
            uploadFileElment: function (content) {
                var form = new FormData()
                form.append('name', content.file.name)
                form.append('file', content.file)
                this.$http.post("/sys/oss/upload?token=" + Cookies.get('token'), form, {
                    timeout: 0,
                    headers: {"Content-Type": "multipart/form-data"}
                }).then(function (res) {
                    content.onSuccess(res)
                }, function (error) {
                    content.onError(error)
                })
            },
            // 表单提交
            dataFormSubmitHandle: debounce(function () {
                this.$refs['dataForm'].validate((valid) => {
                    if (!valid) {
                        return false
                    }
                    this.$http[!this.dataForm.id ? 'post' : 'put']('/vip/shop/', this.dataForm).then(({data: res}) => {
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
