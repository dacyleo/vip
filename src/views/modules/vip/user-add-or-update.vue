<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
          <el-form-item label="手机号码" prop="phone">
          <el-input v-model="dataForm.phone" placeholder="手机号码"></el-input>
      </el-form-item>
          <el-form-item label="用户类型: 1普通用户, 2会员" prop="type">
          <el-input v-model="dataForm.type" placeholder="用户类型: 1普通用户, 2会员"></el-input>
      </el-form-item>
          <el-form-item label="昵称" prop="nickname">
          <el-input v-model="dataForm.nickname" placeholder="昵称"></el-input>
      </el-form-item>
          <el-form-item label="备注" prop="remark">
          <el-input v-model="dataForm.remark" placeholder="备注"></el-input>
      </el-form-item>
          <el-form-item label="公众号openId" prop="openId">
          <el-input v-model="dataForm.openId" placeholder="公众号openId"></el-input>
      </el-form-item>
          <el-form-item label="小程序openId" prop="miniOpenId">
          <el-input v-model="dataForm.miniOpenId" placeholder="小程序openId"></el-input>
      </el-form-item>
          <el-form-item label="微信的unionId" prop="unionId">
          <el-input v-model="dataForm.unionId" placeholder="微信的unionId"></el-input>
      </el-form-item>
          <el-form-item label="头像图片地址（默认微信）" prop="headImgUrl">
          <el-input v-model="dataForm.headImgUrl" placeholder="头像图片地址（默认微信）"></el-input>
      </el-form-item>
          <el-form-item label="性别: 1表示男性, 2表示女性" prop="sex">
          <el-input v-model="dataForm.sex" placeholder="性别: 1表示男性, 2表示女性"></el-input>
      </el-form-item>
          <el-form-item label="性别描述信息: 男、女、未知等" prop="sexDesc">
          <el-input v-model="dataForm.sexDesc" placeholder="性别描述信息: 男、女、未知等"></el-input>
      </el-form-item>
          <el-form-item label="语言" prop="language">
          <el-input v-model="dataForm.language" placeholder="语言"></el-input>
      </el-form-item>
          <el-form-item label="省" prop="province">
          <el-input v-model="dataForm.province" placeholder="省"></el-input>
      </el-form-item>
          <el-form-item label="市" prop="city">
          <el-input v-model="dataForm.city" placeholder="市"></el-input>
      </el-form-item>
          <el-form-item label="区" prop="country">
          <el-input v-model="dataForm.country" placeholder="区"></el-input>
      </el-form-item>
          <el-form-item label="创建时间" prop="createTime">
          <el-input v-model="dataForm.createTime" placeholder="创建时间"></el-input>
      </el-form-item>
          <el-form-item label="VIP到期时间" prop="vipValidateTime">
          <el-input v-model="dataForm.vipValidateTime" placeholder="VIP到期时间"></el-input>
      </el-form-item>
          <el-form-item label="成为VIP时间" prop="vipTime">
          <el-input v-model="dataForm.vipTime" placeholder="成为VIP时间"></el-input>
      </el-form-item>
          <el-form-item label="连续签到天数" prop="signAmount">
          <el-input v-model="dataForm.signAmount" placeholder="连续签到天数"></el-input>
      </el-form-item>
          <el-form-item label="抽奖次数" prop="luckyAmount">
          <el-input v-model="dataForm.luckyAmount" placeholder="抽奖次数"></el-input>
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
  data () {
    return {
      visible: false,
      dataForm: {
        id: '',
        phone: '',
        type: '',
        nickname: '',
        remark: '',
        openId: '',
        miniOpenId: '',
        unionId: '',
        headImgUrl: '',
        sex: '',
        sexDesc: '',
        language: '',
        province: '',
        city: '',
        country: '',
        createTime: '',
        vipValidateTime: '',
        vipTime: '',
        signAmount: '',
        luckyAmount: ''
      }
    }
  },
  computed: {
    dataRule () {
      return {
        phone: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        type: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        nickname: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        remark: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        openId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        miniOpenId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        unionId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        headImgUrl: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        sex: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        sexDesc: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        language: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        province: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        city: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        country: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        createTime: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        vipValidateTime: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        vipTime: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        signAmount: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        luckyAmount: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    init () {
      this.visible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].resetFields()
        if (this.dataForm.id) {
          this.getInfo()
        }
      })
    },
    // 获取信息
    getInfo () {
      this.$http.get(`/vip/user/${this.dataForm.id}`).then(({ data: res }) => {
        if (res.code !== 0) {
          return this.$message.error(res.msg)
        }
        this.dataForm = {
          ...this.dataForm,
          ...res.data
        }
      }).catch(() => {})
    },
    // 表单提交
    dataFormSubmitHandle: debounce(function () {
      this.$refs['dataForm'].validate((valid) => {
        if (!valid) {
          return false
        }
        this.$http[!this.dataForm.id ? 'post' : 'put']('/vip/user/', this.dataForm).then(({ data: res }) => {
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
        }).catch(() => {})
      })
    }, 1000, { 'leading': true, 'trailing': false })
  }
}
</script>
