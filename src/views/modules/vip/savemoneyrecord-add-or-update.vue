<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
          <el-form-item label="0外卖1视频2商品券3零元换购" prop="type">
          <el-input v-model="dataForm.type" placeholder="0外卖1视频2商品券3零元换购"></el-input>
      </el-form-item>
          <el-form-item label="领取人ID" prop="uid">
          <el-input v-model="dataForm.uid" placeholder="领取人ID"></el-input>
      </el-form-item>
          <el-form-item label="领用名称" prop="name">
          <el-input v-model="dataForm.name" placeholder="领用名称"></el-input>
      </el-form-item>
          <el-form-item label="价值" prop="money">
          <el-input v-model="dataForm.money" placeholder="价值"></el-input>
      </el-form-item>
          <el-form-item label="领取时间" prop="createTime">
          <el-input v-model="dataForm.createTime" placeholder="领取时间"></el-input>
      </el-form-item>
          <el-form-item label="关联ID" prop="refId">
          <el-input v-model="dataForm.refId" placeholder="关联ID"></el-input>
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
        type: '',
        uid: '',
        name: '',
        money: '',
        createTime: '',
        refId: ''
      }
    }
  },
  computed: {
    dataRule () {
      return {
        type: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        uid: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        name: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        money: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        createTime: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        refId: [
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
      this.$http.get(`/vip/savemoneyrecord/${this.dataForm.id}`).then(({ data: res }) => {
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
        this.$http[!this.dataForm.id ? 'post' : 'put']('/vip/savemoneyrecord/', this.dataForm).then(({ data: res }) => {
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
