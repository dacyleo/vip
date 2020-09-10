<template>
    <el-dialog width="80%" :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')"
               @close="closedDialog"
               :close-on-click-modal="false" :close-on-press-escape="false">
        <el-form
                :model="dataForm"
                :rules="dataRule"
                ref="dataForm"
                :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'"
        >
            <!--          <el-form-item label="状态: 1未上架, 2已上架, 3删除" prop="status">-->
            <!--          <el-input v-model="dataForm.status" placeholder="状态: 1未上架, 2已上架, 3删除"></el-input>-->
            <!--      </el-form-item>-->
            <el-form-item label="标题" prop="title">
                <el-input v-model="dataForm.title" placeholder="标题" style="width: 400px"></el-input>
            </el-form-item>
<!--            <el-form-item label="简介" prop="intro">-->
<!--                <el-input style="width: 600px" v-model="dataForm.intro" type="textarea" rows="4"-->
<!--                          placeholder="简介"></el-input>-->
<!--            </el-form-item>-->
            <el-form-item label="简介">
                <!-- 富文本编辑器, 容器 -->
                <div id="J_quillEditor" style="height: 400px;"></div>
                <!-- 自定义上传图片功能 (使用element upload组件) -->
                <el-upload
                        :action="uploadUrl"
                        :show-file-list="false"
                        :before-upload="uploadBeforeUploadHandle"
                        :on-success="uploadSuccessHandle"
                        style="display: none;">
                    <el-button ref="uploadBtn" type="primary" size="small">上传</el-button>
                </el-upload>
            </el-form-item>
            <el-form-item label="封面图片" prop="coverImgUrl">
                <el-upload
                        class="avatar-uploader"
                        action=""
                        :http-request="uploadFileElment"
                        :show-file-list="false"
                        :on-success="handleSuccess"
                        :before-upload="beforeUpload">
                    <img style="width: 120px; height: 100px;" v-if="dataForm.coverImgUrl" :src="dataForm.coverImgUrl">
                    <img v-else style="width: 120px; height: 100px;" src="../../../assets/img/update.png">
                </el-upload>
            </el-form-item>
            <el-form-item label="视频列表" prop="videos">
                <!--                <template v-for="(item,index) in avList" v-if="showAvList">-->
                <!--                    <video :id="'ids'+index" width="414" height="270" preload="auto" playsinline webkit-playsinline></video>-->
                <!--                </template>-->
                <!--                <el-upload-->
                <!--                        class="avatar-uploader"-->
                <!--                        action=""-->
                <!--                        :http-request="uploadAvFileElment"-->
                <!--                        :show-file-list="false"-->
                <!--                        :on-success="handleAvSuccess"-->
                <!--                        :before-upload="beforeAvUpload">-->
                <!--                    <img style="width: 140px; height: 100px;"-->
                <!--                         src="../../../assets/img/upload2.jpg">-->
                <!--                </el-upload>-->
                <!--                <el-button type="small" @click="cleanPic()">删除</el-button>-->
                <course-video :data-form="dataForm" ref="courseVideo"></course-video>
            </el-form-item>
            <el-form-item label="合伙人" prop="uidName">
                <el-input v-model="dataForm.uidName" readonly placeholder="合伙人" style="width:150px;"></el-input>
                <!--              <el-button icon="el-icon-search" @click="chooseUser()" circle>选择</el-button>-->
                &nbsp;&nbsp;
                <el-button type="primary" icon="el-icon-search" @click="chooseUser()">选择</el-button>
            </el-form-item>
            <el-row>
                <el-col :span="16">
                    <el-form-item label="所在城市" prop="provinceCode">
                        <el-select v-model="dataForm.provinceCode" placeholder="省"
                                   @change="getInitCityList(dataForm.provinceCode)" clearable>
                            <el-option label="请选择" value=""></el-option>
                            <el-option v-for="pro in provinceList" :label="pro.name" :value="pro.code"
                                       :key="pro.id"></el-option>
                        </el-select>
                        &nbsp;&nbsp;
                        <el-select v-model="dataForm.cityCode" placeholder="市" clearable>
                            <el-option label="请选择" value=""></el-option>
                            <el-option v-for="city in cityList" :label="city.name" :value="city.code"
                                       :key="city.id"></el-option>
                        </el-select>
                    </el-form-item>
                </el-col>
                <el-col :span="8">
                    <el-form-item label="视频时长(分钟)" prop="videoTime" label-width="120px">
                        <el-input v-model="dataForm.videoTime" placeholder="总视频时长(分钟)"></el-input>
                    </el-form-item>
                </el-col>
            </el-row>
            <el-row>
                <el-col :span="6">
                    <el-form-item label="划线金额" prop="markMoney">
                        <el-input v-model="dataForm.markMoney" placeholder="划线金额"></el-input>
                    </el-form-item>
                </el-col>
                <el-col :span="6">
                    <el-form-item label="实际金额" prop="money">
                        <el-input v-model="dataForm.money" placeholder="实际金额"></el-input>
                    </el-form-item>
                </el-col>
                <el-col :span="6">
                    <el-form-item label="播放次数" prop="playTimes">
                        <el-input v-model="dataForm.playTimes" placeholder="播放次数"></el-input>
                    </el-form-item>
                </el-col>
                <el-col :span="6">
                    <el-form-item label="点赞次数" prop="likeTimes">
                        <el-input v-model="dataForm.likeTimes" placeholder="点赞次数"></el-input>
                    </el-form-item>
                </el-col>
            </el-row>
            <el-row>
                <el-col :span="6">
                    <el-form-item label="试看时间(秒)" prop="tryExpireTime" label-width="100px">
                        <el-input v-model="dataForm.tryExpireTime" placeholder="试看时间(秒)"></el-input>
                    </el-form-item>
                </el-col>
                <el-col :span="6">
                    <el-form-item label="购买期限(天)" prop="buyExpireTime" label-width="100px">
                        <el-input v-model="dataForm.buyExpireTime" placeholder="购买有效期(天)"></el-input>
                    </el-form-item>
                </el-col>
                <el-col :span="6">
                    <el-form-item label="可以分享" prop="canShare">
                        <el-select v-model="dataForm.canShare" placeholder="是否可以分享" clearable>
                            <el-option label="是" :value="1"></el-option>
                            <el-option label="否" :value="2"></el-option>
                        </el-select>
                    </el-form-item>
                </el-col>
                <el-col :span="6">
                    <el-form-item label="显示顺序" prop="showOrder">
                        <el-input v-model="dataForm.showOrder" placeholder="显示顺序（正序）"></el-input>
                    </el-form-item>
                </el-col>
            </el-row>
            <el-row>
                <el-col :span="6">
                    <el-form-item label="合伙占比" prop="partnerProfitRate">
                        <el-input v-model="dataForm.partnerProfitRate" placeholder="合伙人收益比"></el-input>
                    </el-form-item>
                </el-col>
                <el-col :span="6">
                    <el-form-item label="一级占比" prop="profitRate">
                        <el-input v-model="dataForm.profitRate" placeholder="一级收益比"></el-input>
                    </el-form-item>
                </el-col>
                <el-col :span="6">
                    <el-form-item label="二级占比" prop="profitRate2">
                        <el-input v-model="dataForm.profitRate2" placeholder="二级收益比"></el-input>
                    </el-form-item>
                </el-col>
                <el-col :span="6">
                    <el-form-item label="三级占比" prop="profitRate3">
                        <el-input v-model="dataForm.profitRate3" placeholder="三级收益比"></el-input>
                    </el-form-item>
                </el-col>
            </el-row>

            <!--          <el-form-item label="店铺收益比（1.0 - 合伙人 - 一级 - 二级 - 三级）" prop="shopProfitRate">-->
            <!--          <el-input v-model="dataForm.shopProfitRate" placeholder="店铺收益比（1.0 - 合伙人 - 一级 - 二级 - 三级）"></el-input>-->
            <!--      </el-form-item>-->
            <!--          <el-form-item label="累计订单数量" prop="orders">-->
            <!--          <el-input v-model="dataForm.orders" placeholder="累计订单数量"></el-input>-->
            <!--      </el-form-item>-->
            <!--          <el-form-item label="累计订单销售金额" prop="orderMoneys">-->
            <!--          <el-input v-model="dataForm.orderMoneys" placeholder="累计订单销售金额"></el-input>-->
            <!--      </el-form-item>-->
            <!--          <el-form-item label="播放次数(真实)" prop="plays">-->
            <!--          <el-input v-model="dataForm.plays" placeholder="播放次数(真实)"></el-input>-->
            <!--      </el-form-item>-->
            <!--         -->
            <el-form-item label="备注" prop="remark">
                <el-input v-model="dataForm.remark" style="width:500px" rows="3" type="textarea"
                          placeholder="备注"></el-input>
            </el-form-item>
            <!--          <el-form-item label="创建时间" prop="createTime">-->
            <!--          <el-input v-model="dataForm.createTime" placeholder="创建时间"></el-input>-->
            <!--      </el-form-item>-->
            <el-form-item>
                <el-button @click="visible = false">{{ $t('cancel') }}</el-button>
                <el-button type="primary" @click="dataFormSubmitHandle()">{{ $t('confirm') }}</el-button>
            </el-form-item>
        </el-form>
        <!--    <template slot="footer">-->
        <!--      <el-button @click="visible = false">{{ $t('cancel') }}</el-button>-->
        <!--      <el-button type="primary" @click="dataFormSubmitHandle()">{{ $t('confirm') }}</el-button>-->
        <!--    </template>-->
        <choose-user v-if="chooseUserVisible" ref="chooseUser" @getUser="confirmChooseUser"></choose-user>
    </el-dialog>
</template>
<script>
    import debounce from 'lodash/debounce'
    import Cookies from 'js-cookie'
    import ChooseUser from './choose-user'
    import CourseVideo from './course-video'
    import 'quill/dist/quill.snow.css'
    import Quill from 'quill'

    export default {
        components: {ChooseUser, CourseVideo},
        data() {
            return {
                uploadUrl: '',
                quillEditor: null,
                quillEditorToolbarOptions: [
                    ['bold', 'italic', 'underline', 'strike'],
                    ['blockquote', 'code-block', 'image'],
                    [{ 'header': 1 }, { 'header': 2 }],
                    [{ 'list': 'ordered' }, { 'list': 'bullet' }],
                    [{ 'script': 'sub' }, { 'script': 'super' }],
                    [{ 'indent': '-1' }, { 'indent': '+1' }],
                    [{ 'direction': 'rtl' }],
                    [{ 'size': ['small', false, 'large', 'huge'] }],
                    [{ 'header': [1, 2, 3, 4, 5, 6, false] }],
                    [{ 'color': [] }, { 'background': [] }],
                    [{ 'font': [] }],
                    [{ 'align': [] }],
                    ['clean']
                ],
                uploadVisible: false,
                chooseUserVisible: false,
                visible: false,
                avList: [],
                titleList: [],
                provinceList: [],
                cityList: [],
                dataForm: {
                    id: '',
                    type: '',
                    status: '',
                    canShare: 2,
                    uidId: '',
                    uidName: '',
                    title: '',
                    intro: '',
                    coverImgUrl: '',
                    videos: '',
                    videoTime: '',
                    province: '',
                    provinceCode: '',
                    city: '',
                    cityCode: '',
                    tryExpireTime: 120,
                    buyExpireTime: 45,
                    showOrder: '',
                    playTimes: 0,
                    likeTimes: 0,
                    markMoney: '',
                    money: '',
                    partnerProfitRate: 0.1,
                    profitRate: 0.4,
                    profitRate2: 0.1,
                    profitRate3: 0,
                    shopProfitRate: '',
                    orders: '',
                    orderMoneys: '',
                    plays: '',
                    likes: '',
                    remark: '',
                    createTime: ''
                }
            }
        },
        computed: {
            dataRule() {
                return {
                    videoTime: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ],
                    canShare: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ],
                    uidId: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ],
                    uidName: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ],
                    title: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ],
                    coverImgUrl: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ],
                    videos: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ],
                    provinceCode: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ],
                    cityCode: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ],
                    tryExpireTime: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ],
                    buyExpireTime: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ],
                    showOrder: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ],
                    playTimes: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ],
                    likeTimes: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ],
                    markMoney: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ],
                    money: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ],
                    partnerProfitRate: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ],
                    profitRate: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ],
                    profitRate2: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ],
                    profitRate3: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ]
                }
            }
        },
        mounted () {
            const s = document.createElement('script')










            s.type = 'text/javascript'
            s.src = '//imgcache.qq.com/open/qcloud/video/tcplayer/ie8/videojs-ie8.js'
            const s2 = document.createElement('script')
            s2.type = 'text/javascript'
            s2.src = '//imgcache.qq.com/open/qcloud/video/tcplayer/lib/hls.min.0.8.8.js'
            const s3 = document.createElement('script')
            s3.type = 'text/javascript'
            s3.src = '//imgcache.qq.com/open/qcloud/video/tcplayer/tcplayer.min.js'
            const s4 = document.createElement('link')
            s4.type = 'text/css'
            s4.rel = 'stylesheet'
            s4.href = '//imgcache.qq.com/open/qcloud/video/tcplayer/tcplayer.css'
            document.getElementsByTagName('head')[0].appendChild(s)
            document.getElementsByTagName('head')[0].appendChild(s2)
            document.getElementsByTagName('head')[0].appendChild(s3)
            document.getElementsByTagName('head')[0].appendChild(s4)
            // console.info('加载播放器JS')
        },
        methods: {
            getInitCityList(code) {
                var that = this
                this.$http.get(`/av/areas/cityByPCode?code=` + code).then(({data: res}) => {
                    if (res.code !== 0) {
                        return this.$message.error(res.msg)
                    }
                    that.cityList = res.data
                    that.dataForm.city = ''
                    that.dataForm.cityCode = ''
                }).catch(() => {
                })
            },
            getCityList(code) {
                var that = this
                this.$http.get(`/av/areas/cityByPCode?code=` + code).then(({data: res}) => {
                    if (res.code !== 0) {
                        return this.$message.error(res.msg)
                    }
                    that.cityList = res.data
                }).catch(() => {
                })
            },
            // 富文本编辑器
            quillEditorHandle () {
                this.quillEditor = new Quill('#J_quillEditor', {
                    modules: {
                        toolbar: this.quillEditorToolbarOptions
                    },
                    theme: 'snow'
                })
                // 自定义上传图片功能 (使用element upload组件)
                this.uploadUrl = `${window.SITE_CONFIG['apiURL']}/sys/oss/upload?token=${Cookies.get('token')}`
                this.quillEditor.getModule('toolbar').addHandler('image', () => {
                    this.$refs.uploadBtn.$el.click()
                })
                // 监听内容变化，动态赋值
                this.quillEditor.on('text-change', () => {
                    this.dataForm.intro = this.quillEditor.root.innerHTML
                })
            },
            // 上传图片之前
            uploadBeforeUploadHandle (file) {
                if (file.type !== 'image/jpg' && file.type !== 'image/jpeg' && file.type !== 'image/png' && file.type !== 'image/gif') {
                    this.$message.error(this.$t('upload.tip', { 'format': 'jpg、png、gif' }))
                    return false
                }
            },
            // 上传图片成功
            uploadSuccessHandle (res, file, fileList) {
                if (res.code !== 0) {
                    return this.$message.error(res.msg)
                }
                this.quillEditor.insertEmbed(this.quillEditor.getSelection().index, 'image', res.data.src)
            },
            init() {
                this.visible = true
                this.$nextTick(() => {
                    if (this.quillEditor) {
                        this.quillEditor.deleteText(0, this.quillEditor.getLength())
                    } else {
                        this.quillEditorHandle()
                    }
                })
                var that = this
                this.$http.get(`/av/areas/top`).then(({data: res}) => {
                    if (res.code !== 0) {
                        return this.$message.error(res.msg)
                    }
                    that.provinceList = res.data
                }).catch(() => {
                })
                this.$nextTick(() => {
                    this.$refs['dataForm'].resetFields()
                    if (this.dataForm.id) {
                        this.getInfo()
                    } else {
                        that.dataForm = {
                            id: '',
                            type: '',
                            status: '',
                            canShare: 2,
                            uidId: '',
                            uidName: '',
                            title: '',
                            intro: '',
                            coverImgUrl: '',
                            videos: '',
                            videoTime: '',
                            province: '',
                            provinceCode: '',
                            city: '',
                            cityCode: '',
                            tryExpireTime: 120,
                            buyExpireTime: 45,
                            showOrder: '',
                            playTimes: 0,
                            likeTimes: 0,
                            markMoney: '',
                            money: '',
                            partnerProfitRate: 0.1,
                            profitRate: 0.4,
                            profitRate2: 0.1,
                            profitRate3: 0,
                            shopProfitRate: '',
                            orders: '',
                            orderMoneys: '',
                            plays: '',
                            likes: '',
                            remark: '',
                            createTime: ''
                        }
                    }
                })
            },
            // 获取信息
            getInfo() {
                this.$http.get(`/av/course/${this.dataForm.id}`).then(({data: res}) => {
                    if (res.code !== 0) {
                        return this.$message.error(res.msg)
                    }
                    this.dataForm = res.data
                    this.quillEditor.root.innerHTML = this.dataForm.intro
                    // console.info("dataFormINFO=", JSON.stringify(this.dataForm, '', ' '))
                    this.getCityList(this.dataForm.provinceCode)
                }).catch(() => {
                })
            },
            // 表单提交
            dataFormSubmitHandle: debounce(function () {
                this.dataForm.videos = JSON.stringify(this.$refs.courseVideo.videos)
                this.dataForm.videoNum = this.$refs.courseVideo.videos.length
                if(!this.dataForm.intro || this.dataForm.intro === ''){
                    this.$message.error("视频简介不能为空")
                    return false
                }
                this.$refs['dataForm'].validate((valid) => {
                    if (!valid) {
                        return false
                    }
                    if (!this.$refs.courseVideo.videos || this.$refs.courseVideo.videos.length === 0) {
                        this.$message.error("视频数量不能为空")
                        return false
                    }
                    var bool = false
                    this.$refs.courseVideo.videos.forEach(item => {
                        if (!item.name || item.name === '') {
                            this.$message.error("视频名称不能为空")
                            bool = true
                        }
                    })
                    if(bool){
                        return false
                    }
                    this.$http[!this.dataForm.id ? 'post' : 'put']('/av/course/', this.dataForm).then(({data: res}) => {
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
            }, 1000, {'leading': true, 'trailing': false}),

            handleSuccess: function (res, file) {
                this.dataForm.coverImgUrl = res.data.data.src
            },
            beforeUpload: function (file) {
                var isJPG = (file.type === 'image/jpeg' || file.type === 'image/png')
                var isLt2M = file.size / 1024 / 1024 < 2

                if (!isJPG) {
                    this.$message.error('上传图片格式只能是JPG/JPEG/PNG/三种格式！')
                }
                if (!isLt2M) {
                    this.$message.error('上传图片大小不能超过 2MB!')
                }
                return isJPG && isLt2M
            },
            uploadFileElment: function (content) {
                var form = new FormData()
                form.append('name', content.file.name)
                form.append('file', content.file)
                this.$http.post('/sys/oss/upload?token=' + Cookies.get('token'), form, {
                    timeout: 0,
                    headers: {'Content-Type': 'multipart/form-data'}
                }).then(function (res) {
                    content.onSuccess(res)
                }, function (error) {
                    content.onError(error)
                })
            },

            handleAvExceed(files, fileList) {
                this.$message.warning(`当前限制选择 5 个文件，本次选择了 ${files.length} 个文件，共选择了 ${files.length + fileList.length} 个文件`)
            },
            chooseUser: function () {
                this.chooseUserVisible = true
                this.$nextTick(() => {
                    this.$refs.chooseUser.init()
                })
            },
            confirmChooseUser: function (data) {
                this.dataForm.uidId = data.id
                this.dataForm.uidName = data.nickname
            },

            // 关闭弹框时，不要继续播放视频
            closedDialog() {
                console.info("不要继续播放视频")
                this.dataForm.videos = '';
                this.$refs.courseVideo.clean();
            }
        }
    }

</script>
