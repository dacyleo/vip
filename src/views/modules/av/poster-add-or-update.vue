<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
<!--          <el-form-item label="海报生成器对应的id" prop="posterId">-->
<!--          <el-input v-model="dataForm.posterId" placeholder="海报生成器对应的id"></el-input>-->
<!--      </el-form-item>-->
          <el-form-item label="类型" prop="type">
              <el-select v-model="dataForm.type" placeholder="类型">
                  <el-option label="有头像" :value="1"></el-option>
                  <el-option label="无头像" :value="2"></el-option>
              </el-select>
<!--          <el-input v-model="dataForm.type" placeholder="类型：1 有头像 , 2 无头像"></el-input>-->
      </el-form-item>
          <el-form-item label="背景图片" prop="imgUrl">
              <el-upload
                      class="avatar-uploader"
                      action=""
                      :http-request="uploadFileElment"
                      :show-file-list="false"
                      :on-success="handleSuccess"
                      :before-upload="beforeUpload">
                  <img style="width: 100px;"
                       v-if="dataForm.imgUrl" :src="dataForm.imgUrl">
                  <img style="width: 100px;" v-else
                       src="../../../assets/img/update.png">
              </el-upload>
<!--          <el-input v-model="dataForm.imgUrl" placeholder="背景图片地址"></el-input>-->
      </el-form-item>
<!--          <el-form-item label="状态: 1未上架, 2已上架, 3删除" prop="status">-->
<!--          <el-input v-model="dataForm.status" placeholder="状态: 1未上架, 2已上架, 3删除"></el-input>-->
<!--      </el-form-item>-->
          <el-form-item label="标题" prop="title">
          <el-input v-model="dataForm.title" placeholder="标题"></el-input>
      </el-form-item>
          <el-form-item label="显示顺序" prop="showOrder">
          <el-input v-model="dataForm.showOrder" placeholder="显示顺序"></el-input>
      </el-form-item>
          <el-form-item label="备注" prop="remark">
              <el-input type="textarea" rows="3" v-model="dataForm.remark" placeholder="备注"></el-input>
      </el-form-item>
<!--          <el-form-item label="创建时间" prop="createTime">-->
<!--          <el-input v-model="dataForm.createTime" placeholder="创建时间"></el-input>-->
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
import Cookies from 'js-cookie'
export default {
  data () {
    return {
      visible: false,
      dataForm: {
        id: '',
        posterId: '',
        type: 1,
        imgUrl: '',
        status: '',
        title: '',
        showOrder: 0,
        remark: '',
        createTime: ''
      }
    }
  },
  computed: {
    dataRule () {
      return {
        posterId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        type: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        imgUrl: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        status: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        title: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        showOrder: [
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
      this.$http.get(`/av/poster/${this.dataForm.id}`).then(({ data: res }) => {
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
        this.$http[!this.dataForm.id ? 'post' : 'put']('/av/poster/', this.dataForm).then(({ data: res }) => {
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
    }, 1000, { 'leading': true, 'trailing': false }),
      handleSuccess: function (res, file) {
          this.dataForm.imgUrl = res.data.data.src
      },
      beforeUpload: function (file) {
          var isJPG = (file.type === 'image/jpeg' || file.type === 'image/png')
          var isLt2M = file.size / 1024 / 1024 < 2

          if (!isJPG) {
              this.$message.error("上传图片格式只能是JPG/JPEG/PNG/三种格式！")
          }
          if (!isLt2M) {
              this.$message.error("上传图片大小不能超过 2MB!")
          }
          return isJPG && isLt2M
      },
      uploadFileElment: function (content) {
          var form = new FormData()
          form.append('name', content.file.name)
          form.append('file', content.file)
          this.$http.post("/sys/oss/upload?token=" + Cookies.get('token'), form, {
              timeout: 0,
              headers: {"Content-Type": "multipart/form-data"}
          }).then(function (res) {
              content.onSuccess(res)
          }, function (error) {
              content.onError(error)
          })
      }
  }
}
</script>
