<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
          <el-form-item label="用户ID" prop="uid">
          <el-input v-model="dataForm.uid" placeholder="用户ID"></el-input>
      </el-form-item>
          <el-form-item label="资金类型" prop="type">
              <el-select v-model="dataForm.type" placeholder="资金类型">
                  <el-option label="提现" :value="1"></el-option>
                  <el-option label="退款" :value="2"></el-option>
                  <el-option label="工资" :value="3"></el-option>
              </el-select>
<!--          <el-input v-model="dataForm.type" placeholder="资金类型"></el-input>-->
      </el-form-item>
          <el-form-item label="状态" prop="status">
              <el-select v-model="dataForm.status"  placeholder="状态">
                  <el-option label="未审核" :value="1"></el-option>
                  <el-option label="审核通过" :value="2"></el-option>
                  <el-option label="审核不通过" :value="3"></el-option>
              </el-select>
<!--          <el-input v-model="dataForm.status" placeholder="状态"></el-input>-->
      </el-form-item>
          <el-form-item label="订单ID" prop="orderId">
          <el-input v-model="dataForm.orderId" placeholder="订单ID"></el-input>
      </el-form-item>
          <el-form-item label="金额" prop="money">
          <el-input v-model="dataForm.money" placeholder="金额"></el-input>
      </el-form-item>
          <el-form-item label="资金状态" prop="moneyStatus">
              <el-select v-model="dataForm.moneyStatus"  placeholder="资金状态">
                  <el-option label="未处理" :value="1"></el-option>
                  <el-option label="已处理" :value="2"></el-option>
              </el-select>
        </el-form-item>
          <el-form-item label="内容(退款原因)" prop="content">
          <el-input v-model="dataForm.content" placeholder="内容(退款原因)"></el-input>
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
        status: '',
        orderId: '',
        money: '',
          moneyStatus:'',
        content: '',
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
        status: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        orderId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        money: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        content: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        // remark: [
        //   { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        // ],
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
      this.$http.get(`/av/fund/${this.dataForm.id}`).then(({ data: res }) => {
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
        this.$http[!this.dataForm.id ? 'post' : 'put']('/av/fund/', this.dataForm).then(({ data: res }) => {
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
