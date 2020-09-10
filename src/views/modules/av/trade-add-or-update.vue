<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
          <el-form-item label="用户ID" prop="uid">
          <el-input v-model="dataForm.uid" placeholder="用户ID"></el-input>
      </el-form-item>
          <el-form-item label="交易类型" prop="type">
          <el-input v-model="dataForm.type" placeholder="交易类型"></el-input>
      </el-form-item>
          <el-form-item label="关联ID" prop="refId">
          <el-input v-model="dataForm.refId" placeholder="关联ID"></el-input>
      </el-form-item>
          <el-form-item label="内部交易编号" prop="orderNo">
          <el-input v-model="dataForm.orderNo" placeholder="内部交易编号"></el-input>
      </el-form-item>
          <el-form-item label="外部交易编号(支付宝相关)" prop="tradeNo">
          <el-input v-model="dataForm.tradeNo" placeholder="外部交易编号(支付宝相关)"></el-input>
      </el-form-item>
          <el-form-item label="用户余额(交易前)" prop="beforeBalance">
          <el-input v-model="dataForm.beforeBalance" placeholder="用户余额(交易前)"></el-input>
      </el-form-item>
          <el-form-item label="金额" prop="money">
          <el-input v-model="dataForm.money" placeholder="金额"></el-input>
      </el-form-item>
          <el-form-item label="用户余额(交易后)" prop="balance">
          <el-input v-model="dataForm.balance" placeholder="用户余额(交易后)"></el-input>
      </el-form-item>
          <el-form-item label="备注" prop="remark">
          <el-input v-model="dataForm.remark" placeholder="备注"></el-input>
      </el-form-item>
          <el-form-item label="创建时间" prop="createTime">
          <el-input v-model="dataForm.createTime" placeholder="创建时间"></el-input>
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
        uid: '',
        type: '',
        refId: '',
        orderNo: '',
        tradeNo: '',
        beforeBalance: '',
        money: '',
        balance: '',
        remark: '',
        createTime: ''
      }
    }
  },
  computed: {
    dataRule () {
      return {
        uid: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        type: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        refId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        orderNo: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        tradeNo: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        beforeBalance: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        money: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        balance: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        remark: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        createTime: [
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
      this.$http.get(`/av/trade/${this.dataForm.id}`).then(({ data: res }) => {
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
        this.$http[!this.dataForm.id ? 'post' : 'put']('/av/trade/', this.dataForm).then(({ data: res }) => {
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
