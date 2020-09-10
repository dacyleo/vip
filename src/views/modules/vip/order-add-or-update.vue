<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
          <el-form-item label="用户ID" prop="uid">
          <el-input v-model="dataForm.uid" placeholder="用户ID"></el-input>
      </el-form-item>
          <el-form-item label="关联ID" prop="refId">
          <el-input v-model="dataForm.refId" placeholder="关联ID"></el-input>
      </el-form-item>
          <el-form-item label="订单编号" prop="orderNo">
          <el-input v-model="dataForm.orderNo" placeholder="订单编号"></el-input>
      </el-form-item>
          <el-form-item label="订单类型: 1购买VIP" prop="type">
          <el-input v-model="dataForm.type" placeholder="订单类型: 1购买VIP"></el-input>
      </el-form-item>
          <el-form-item label="订单状态: 1待付款, 2已完成" prop="status">
          <el-input v-model="dataForm.status" placeholder="订单状态: 1待付款, 2已完成"></el-input>
      </el-form-item>
          <el-form-item label="支付金额" prop="payMoney">
          <el-input v-model="dataForm.payMoney" placeholder="支付金额"></el-input>
      </el-form-item>
          <el-form-item label="支付状态: 1未支付, 2已支付" prop="payStatus">
          <el-input v-model="dataForm.payStatus" placeholder="支付状态: 1未支付, 2已支付"></el-input>
      </el-form-item>
          <el-form-item label="支付类型: 0微信付款" prop="payType">
          <el-input v-model="dataForm.payType" placeholder="支付类型: 0微信付款"></el-input>
      </el-form-item>
          <el-form-item label="支付时间" prop="payTime">
          <el-input v-model="dataForm.payTime" placeholder="支付时间"></el-input>
      </el-form-item>
          <el-form-item label="订单备注" prop="remark">
          <el-input v-model="dataForm.remark" placeholder="订单备注"></el-input>
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
        refId: '',
        orderNo: '',
        type: '',
        status: '',
        payMoney: '',
        payStatus: '',
        payType: '',
        payTime: '',
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
        refId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        orderNo: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        type: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        status: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        payMoney: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        payStatus: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        payType: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        payTime: [
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
      this.$http.get(`/vip/order/${this.dataForm.id}`).then(({ data: res }) => {
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
        this.$http[!this.dataForm.id ? 'post' : 'put']('/vip/order/', this.dataForm).then(({ data: res }) => {
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
