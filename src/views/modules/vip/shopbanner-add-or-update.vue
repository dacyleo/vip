<template>
    <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false"
               :close-on-press-escape="false">
        <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()"
                 :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
            <el-form-item label="所属商户" prop="shopId">
                <el-select v-model="dataForm.shopId" placeholder="请选择商户">
                    <el-option v-for="(item,index) in shopList" :key="index" :label="item.name"
                               :value="item.id"></el-option>
                </el-select>
            </el-form-item>
            <el-form-item label="banner标题" prop="title">
                <el-input v-model="dataForm.title" placeholder="banner标题"></el-input>
            </el-form-item>
            <el-form-item label="banner图片" prop="imageUrl">
                <el-upload
                        class="avatar-uploader"
                        action=""
                        :http-request="uploadFileElment"
                        :show-file-list="false"
                        :on-success="handleSuccess"
                        :before-upload="beforeUpload">
                    <img style="width: 94px;height: 94px;"
                         v-if="dataForm.imageUrl" :src="dataForm.imageUrl">
                    <img style="width: 94px;height: 94px;" v-else
                         src="../../../assets/img/update.png">
                </el-upload>
            </el-form-item>
            <el-form-item label="banner链接URL" prop="refUrl">
                <el-input v-model="dataForm.refUrl" placeholder="banner链接URL"></el-input>
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
                    shopId: '',
                    title: '',
                    imageUrl: '',
                    refUrl: '',
                    state: '',
                    createTime: ''
                },
                shopList: []
            }
        },
        computed: {
            dataRule() {
                return {
                    shopId: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ],
                    title: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ],
                    imageUrl: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ]
                }
            }
        },
        methods: {
            init() {
                this.visible = true
                this.getShop()
                this.$nextTick(() => {
                    this.$refs['dataForm'].resetFields()
                    if (this.dataForm.id) {
                        this.getInfo()
                    }
                })
            },
            getShop() {
                this.$http.get(`/vip/shop/list`).then(({data: res}) => {
                    if (res.code !== 0) {
                        return this.$message.error(res.msg)
                    }
                    this.shopList = {
                        ...this.shopList,
                        ...res.data
                    }
                }).catch(() => {
                })
            },
            // 获取信息
            getInfo() {
                this.$http.get(`/vip/shopbanner/${this.dataForm.id}`).then(({data: res}) => {
                    if (res.code !== 0) {
                        return this.$message.error(res.msg)
                    }
                    this.dataForm = {
                        ...this.dataForm,
                        ...res.data
                    }
                }).catch(() => {
                })
            },
            handleSuccess: function (res, file) {
                this.dataForm.imageUrl = res.data.data.src
            },
            beforeUpload: function (file) {
                var isJPG = (file.type === 'image/jpeg' || file.type === 'image/png')
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
                    this.$http[!this.dataForm.id ? 'post' : 'put']('/vip/shopbanner/', this.dataForm).then(({data: res}) => {
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
