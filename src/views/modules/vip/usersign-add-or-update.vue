<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
          <el-form-item label="签到用户ID" prop="uid">
          <el-input v-model="dataForm.uid" placeholder="签到用户ID"></el-input>
      </el-form-item>
          <el-form-item label="签到时间" prop="signTime">
          <el-input v-model="dataForm.signTime" placeholder="签到时间"></el-input>
      </el-form-item>
          <el-form-item label="签到日期" prop="signDate">
          <el-input v-model="dataForm.signDate" placeholder="签到日期"></el-input>
      </el-form-item>
          <el-form-item label="签到月份" prop="signMonth">
          <el-input v-model="dataForm.signMonth" placeholder="签到月份"></el-input>
      </el-form-item>
          <el-form-item label="连续签到天数" prop="amount">
          <el-input v-model="dataForm.amount" placeholder="连续签到天数"></el-input>
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
        signTime: '',
        signDate: '',
        signMonth: '',
        amount: ''
      }
    }
  },
  computed: {
    dataRule () {
      return {
        uid: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        signTime: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        signDate: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        signMonth: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        amount: [
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
      this.$http.get(`/vip/usersign/${this.dataForm.id}`).then(({ data: res }) => {
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
        this.$http[!this.dataForm.id ? 'post' : 'put']('/vip/usersign/', this.dataForm).then(({ data: res }) => {
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
