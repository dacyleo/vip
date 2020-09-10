<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
          <el-form-item label="用户ID" prop="uid">
          <el-input v-model="dataForm.uid" placeholder="用户ID"></el-input>
      </el-form-item>
          <el-form-item label="获得抽奖机会时间" prop="createTime">
          <el-input v-model="dataForm.createTime" placeholder="获得抽奖机会时间"></el-input>
      </el-form-item>
          <el-form-item label="关联奖品ID" prop="refId">
          <el-input v-model="dataForm.refId" placeholder="关联奖品ID"></el-input>
      </el-form-item>
          <el-form-item label="中奖产品名称" prop="refName">
          <el-input v-model="dataForm.refName" placeholder="中奖产品名称"></el-input>
      </el-form-item>
          <el-form-item label="0机会未使用，1机会已使用" prop="state">
          <el-input v-model="dataForm.state" placeholder="0机会未使用，1机会已使用"></el-input>
      </el-form-item>
          <el-form-item label="抽奖时间" prop="updateTime">
          <el-input v-model="dataForm.updateTime" placeholder="抽奖时间"></el-input>
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
        createTime: '',
        refId: '',
        refName: '',
        state: '',
        updateTime: ''
      }
    }
  },
  computed: {
    dataRule () {
      return {
        uid: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        createTime: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        refId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        refName: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        state: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        updateTime: [
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
      this.$http.get(`/vip/luckyrecord/${this.dataForm.id}`).then(({ data: res }) => {
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
        this.$http[!this.dataForm.id ? 'post' : 'put']('/vip/luckyrecord/', this.dataForm).then(({ data: res }) => {
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
