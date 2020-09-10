<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
          <el-form-item label="优惠券金额" prop="price">
          <el-input v-model="dataForm.price" placeholder="优惠券金额"></el-input>
      </el-form-item>
          <el-form-item label="商户ID" prop="shopId">
          <el-input v-model="dataForm.shopId" placeholder="商户ID"></el-input>
      </el-form-item>
          <el-form-item label="优惠券简介" prop="title">
          <el-input v-model="dataForm.title" placeholder="优惠券简介"></el-input>
      </el-form-item>
          <el-form-item label="优惠券说明" prop="content">
          <el-input v-model="dataForm.content" placeholder="优惠券说明"></el-input>
      </el-form-item>
          <el-form-item label="领取个数" prop="drawAmount">
          <el-input v-model="dataForm.drawAmount" placeholder="领取个数"></el-input>
      </el-form-item>
          <el-form-item label="核销个数" prop="verifyAmount">
          <el-input v-model="dataForm.verifyAmount" placeholder="核销个数"></el-input>
      </el-form-item>
          <el-form-item label="" prop="createTime">
          <el-input v-model="dataForm.createTime" placeholder=""></el-input>
      </el-form-item>
          <el-form-item label="0草稿1上架2下架" prop="state">
          <el-input v-model="dataForm.state" placeholder="0草稿1上架2下架"></el-input>
      </el-form-item>
          <el-form-item label="商品图片" prop="picUrl">
          <el-input v-model="dataForm.picUrl" placeholder="商品图片"></el-input>
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
        price: '',
        shopId: '',
        title: '',
        content: '',
        drawAmount: '',
        verifyAmount: '',
        createTime: '',
        state: '',
        picUrl: ''
      }
    }
  },
  computed: {
    dataRule () {
      return {
        price: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        shopId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        title: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        content: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        drawAmount: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        verifyAmount: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        createTime: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        state: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        picUrl: [
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
      this.$http.get(`/vip/shopproduct/${this.dataForm.id}`).then(({ data: res }) => {
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
        this.$http[!this.dataForm.id ? 'post' : 'put']('/vip/shopproduct/', this.dataForm).then(({ data: res }) => {
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
