<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
          <el-form-item label="用户ID" prop="uid">
          <el-input v-model="dataForm.uid" placeholder="用户ID"></el-input>
      </el-form-item>
          <el-form-item label="用户类型: 1课程播放, 2课程访问, 3课程分享, 4课程点赞" prop="type">
          <el-input v-model="dataForm.type" placeholder="用户类型: 1课程播放, 2课程访问, 3课程分享, 4课程点赞"></el-input>
      </el-form-item>
          <el-form-item label="课程ID" prop="courseId">
          <el-input v-model="dataForm.courseId" placeholder="课程ID"></el-input>
      </el-form-item>
          <el-form-item label="订单ID" prop="orderId">
          <el-input v-model="dataForm.orderId" placeholder="订单ID"></el-input>
      </el-form-item>
          <el-form-item label="IP" prop="ip">
          <el-input v-model="dataForm.ip" placeholder="IP"></el-input>
      </el-form-item>
          <el-form-item label="User-Agent" prop="userAgent">
          <el-input v-model="dataForm.userAgent" placeholder="User-Agent"></el-input>
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
        courseId: '',
        orderId: '',
        ip: '',
        userAgent: '',
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
        courseId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        orderId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        ip: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        userAgent: [
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
      this.$http.get(`/av/courselog/${this.dataForm.id}`).then(({ data: res }) => {
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
        this.$http[!this.dataForm.id ? 'post' : 'put']('/av/courselog/', this.dataForm).then(({ data: res }) => {
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
