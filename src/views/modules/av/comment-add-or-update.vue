<template>
  <el-dialog :visible.sync="visible" title="审核" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
<!--          <el-form-item label="用户ID" prop="uid">-->
<!--          <el-input v-model="dataForm.uid" :disabled="true" placeholder="用户ID"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="课程ID" prop="courseId">-->
<!--          <el-input v-model="dataForm.courseId" placeholder="课程ID"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="订单ID" prop="orderId">-->
<!--          <el-input v-model="dataForm.orderId" placeholder="订单ID"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="订单编号" prop="orderNo">-->
<!--          <el-input v-model="dataForm.orderNo" placeholder="订单编号"></el-input>-->
<!--      </el-form-item>-->
          <el-form-item label="评论内容" prop="content">
          <el-input v-model="dataForm.content" :disabled="true" placeholder="评论内容"></el-input>
      </el-form-item>
          <el-form-item label="官方回复" prop="reply">
          <el-input type="textarea" v-model="dataForm.reply" placeholder="官方回复"></el-input>
      </el-form-item>
                  <el-form-item label="状态" prop="status">
                      <el-select v-model="dataForm.status" placeholder="状态">
<!--                          <el-option label="未审核" :disabled="true" :value="1"></el-option>-->
                          <el-option label="审核通过" :value="2"></el-option>
                          <el-option label="审核未通过" :value="3"></el-option>
                      </el-select>
        <!--          <el-input v-model="dataForm.status" placeholder="状态"></el-input>-->
              </el-form-item>
<!--          <el-form-item label="创建时间" prop="createTime">-->
<!--          <el-input v-model="dataForm.createTime" placeholder="创建时间"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="审核时间" prop="auditTime">-->
<!--          <el-input v-model="dataForm.auditTime" placeholder="审核时间"></el-input>-->
<!--      </el-form-item>-->
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
        courseId: '',
        orderId: '',
        orderNo: '',
        status: 2,
        content: '',
        reply: '',
        createTime: '',
        auditTime: ''
      }
    }
  },
  computed: {
    dataRule () {
      return {
        uid: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        courseId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        orderId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        orderNo: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        status: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        content: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        // reply: [
        //   { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        // ],
        createTime: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        auditTime: [
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
      this.$http.get(`/av/comment/${this.dataForm.id}`).then(({ data: res }) => {
        if (res.code !== 0) {
          return this.$message.error(res.msg)
        }
        if(res.data.status === 1){
            res.data.status = 2
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
        this.$http[!this.dataForm.id ? 'post' : 'put']('/av/comment/', this.dataForm).then(({ data: res }) => {
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
