<template>
  <el-dialog :visible.sync="visible" title="提现审核" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" ref="dataForm" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
          <el-form-item label="状态" prop="status">
              <el-select v-model="dataForm.status"  placeholder="状态">
                  <el-option label="审核通过" :value="2"></el-option>
                  <el-option label="审核不通过" :value="3"></el-option>
              </el-select>
          </el-form-item>
          <el-form-item label="备注" prop="remark">
          <el-input v-model="dataForm.remark" type="textarea" placeholder="备注"></el-input>
      </el-form-item>
      </el-form>
    <template slot="footer">
      <el-button @click="visible = false">{{ $t('cancel') }}</el-button>
      <el-button type="primary" @click="withDrawAudit()">{{ $t('confirm') }}</el-button>
    </template>
  </el-dialog>
</template>

<script>
export default {
  data () {
    return {
      visible: false,
      dataForm: {
        id: '',
        status: 2,
        remark: ''
      }
    }
  },
  methods: {
      init() {
          this.visible = true
      },
      withDrawAudit() {
          var that = this
          this.$http.post(`/av/fund/auditDrawMoney`,{"id":this.dataForm.id,"status":this.dataForm.status,"remark":this.dataForm.remark}).then(({data: res}) => {
              if (res.code !== 0) {
                  return that.$message.error(res.msg)
              }else{
                  this.$emit('refreshList')
                  that.visible = false
              }
          }).catch(() => {
          })
      }
  }
}
</script>
