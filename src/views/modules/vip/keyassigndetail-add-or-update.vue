<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
          <el-form-item label="分配ID" prop="assignId">
          <el-input v-model="dataForm.assignId" placeholder="分配ID"></el-input>
      </el-form-item>
          <el-form-item label="系统用户ID（sys_user）" prop="sysUserId">
          <el-input v-model="dataForm.sysUserId" placeholder="系统用户ID（sys_user）"></el-input>
      </el-form-item>
          <el-form-item label="激活码" prop="keyNo">
          <el-input v-model="dataForm.keyNo" placeholder="激活码"></el-input>
      </el-form-item>
          <el-form-item label="0未使用，1已使用" prop="state">
          <el-input v-model="dataForm.state" placeholder="0未使用，1已使用"></el-input>
      </el-form-item>
          <el-form-item label="使用者ID（vip_user）" prop="uid">
          <el-input v-model="dataForm.uid" placeholder="使用者ID（vip_user）"></el-input>
      </el-form-item>
          <el-form-item label="" prop="createTime">
          <el-input v-model="dataForm.createTime" placeholder=""></el-input>
      </el-form-item>
          <el-form-item label="使用时间" prop="usedTime">
          <el-input v-model="dataForm.usedTime" placeholder="使用时间"></el-input>
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
        assignId: '',
        sysUserId: '',
        keyNo: '',
        state: '',
        uid: '',
        createTime: '',
        usedTime: ''
      }
    }
  },
  computed: {
    dataRule () {
      return {
        assignId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        sysUserId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        keyNo: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        state: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        uid: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        createTime: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        usedTime: [
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
      this.$http.get(`/vip/keyassigndetail/${this.dataForm.id}`).then(({ data: res }) => {
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
        this.$http[!this.dataForm.id ? 'post' : 'put']('/vip/keyassigndetail/', this.dataForm).then(({ data: res }) => {
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
