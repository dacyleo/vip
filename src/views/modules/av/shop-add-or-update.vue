<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
          <el-form-item label="商户名称" prop="name">
          <el-input v-model="dataForm.name" placeholder="商户名称"></el-input>
      </el-form-item>
          <el-form-item label="状态: 1未审核, 2审核通过, 3审核不通过" prop="status">
          <el-input v-model="dataForm.status" placeholder="状态: 1未审核, 2审核通过, 3审核不通过"></el-input>
      </el-form-item>
          <el-form-item label="总收益" prop="profitTotal">
          <el-input v-model="dataForm.profitTotal" placeholder="总收益"></el-input>
      </el-form-item>
          <el-form-item label="周收益" prop="profitWeek">
          <el-input v-model="dataForm.profitWeek" placeholder="周收益"></el-input>
      </el-form-item>
          <el-form-item label="日收益" prop="profitDay">
          <el-input v-model="dataForm.profitDay" placeholder="日收益"></el-input>
      </el-form-item>
          <el-form-item label="可用金额" prop="balance">
          <el-input v-model="dataForm.balance" placeholder="可用金额"></el-input>
      </el-form-item>
          <el-form-item label="总工资金额" prop="salarys">
          <el-input v-model="dataForm.salarys" placeholder="总工资金额"></el-input>
      </el-form-item>
          <el-form-item label="累计订单数量(自己的)" prop="orders">
          <el-input v-model="dataForm.orders" placeholder="累计订单数量(自己的)"></el-input>
      </el-form-item>
          <el-form-item label="累计订单金额(自己的)" prop="orderMoneys">
          <el-input v-model="dataForm.orderMoneys" placeholder="累计订单金额(自己的)"></el-input>
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
        name: '',
        status: '',
        profitTotal: '',
        profitWeek: '',
        profitDay: '',
        balance: '',
        salarys: '',
        orders: '',
        orderMoneys: '',
        remark: '',
        createTime: ''
      }
    }
  },
  computed: {
    dataRule () {
      return {
        name: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        status: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        profitTotal: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        profitWeek: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        profitDay: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        balance: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        salarys: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        orders: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        orderMoneys: [
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
      this.$http.get(`/av/shop/${this.dataForm.id}`).then(({ data: res }) => {
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
        this.$http[!this.dataForm.id ? 'post' : 'put']('/av/shop/', this.dataForm).then(({ data: res }) => {
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
