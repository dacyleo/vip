<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
          <el-form-item label="商品ID" prop="productId">
          <el-input v-model="dataForm.productId" placeholder="商品ID"></el-input>
      </el-form-item>
          <el-form-item label="领取人ID" prop="uid">
          <el-input v-model="dataForm.uid" placeholder="领取人ID"></el-input>
      </el-form-item>
          <el-form-item label="领取人openid,生成二维码使用，避免uid被劫持(uid目前是连续的)" prop="openId">
          <el-input v-model="dataForm.openId" placeholder="领取人openid,生成二维码使用，避免uid被劫持(uid目前是连续的)"></el-input>
      </el-form-item>
          <el-form-item label="0未核销1已核销" prop="state">
          <el-input v-model="dataForm.state" placeholder="0未核销1已核销"></el-input>
      </el-form-item>
          <el-form-item label="领取时间" prop="createTime">
          <el-input v-model="dataForm.createTime" placeholder="领取时间"></el-input>
      </el-form-item>
          <el-form-item label="核销时间" prop="updateTime">
          <el-input v-model="dataForm.updateTime" placeholder="核销时间"></el-input>
      </el-form-item>
          <el-form-item label="核销金额" prop="money">
          <el-input v-model="dataForm.money" placeholder="核销金额"></el-input>
      </el-form-item>
          <el-form-item label="核销人ID" prop="adminUid">
          <el-input v-model="dataForm.adminUid" placeholder="核销人ID"></el-input>
      </el-form-item>
          <el-form-item label="领用优惠券标题" prop="title">
          <el-input v-model="dataForm.title" placeholder="领用优惠券标题"></el-input>
      </el-form-item>
          <el-form-item label="领用二维码内容" prop="qrcodeContent">
          <el-input v-model="dataForm.qrcodeContent" placeholder="领用二维码内容"></el-input>
      </el-form-item>
          <el-form-item label="二维码地址" prop="imageUrl">
          <el-input v-model="dataForm.imageUrl" placeholder="二维码地址"></el-input>
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
        productId: '',
        uid: '',
        openId: '',
        state: '',
        createTime: '',
        updateTime: '',
        money: '',
        adminUid: '',
        title: '',
        qrcodeContent: '',
        imageUrl: ''
      }
    }
  },
  computed: {
    dataRule () {
      return {
        productId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        uid: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        openId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        state: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        createTime: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        updateTime: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        money: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        adminUid: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        title: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        qrcodeContent: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        imageUrl: [
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
      this.$http.get(`/vip/productdrawrecord/${this.dataForm.id}`).then(({ data: res }) => {
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
        this.$http[!this.dataForm.id ? 'post' : 'put']('/vip/productdrawrecord/', this.dataForm).then(({ data: res }) => {
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
