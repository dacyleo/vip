<template>
  <el-dialog :visible.sync="visible"  title="修改" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
<!--          <el-form-item label="手机号码" prop="phone">-->
<!--          <el-input v-model="dataForm.phone" placeholder="手机号码"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="用户状态: 1启用, 2禁用, 3逻辑删除" prop="status">-->
<!--          <el-input v-model="dataForm.status" placeholder="用户状态: 1启用, 2禁用, 3逻辑删除"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="用户类型: 1普通用户, 2合伙人" prop="type">-->
<!--          <el-input v-model="dataForm.type" placeholder="用户类型: 1普通用户, 2合伙人"></el-input>-->
<!--      </el-form-item>-->
        <el-form-item label="用户昵称" prop="nickname">
            {{dataForm.nickname}}
        </el-form-item>
        <el-form-item label="手机号码" prop="phone">
            {{dataForm.phone}}
        </el-form-item>
        <el-form-item label="用户头像" prop="headImgUrl">
            <template slot-scope="scope">
                <img :src="dataForm.headImgUrl" width="40" height="40" class="headImgUrl"/>
            </template>
        </el-form-item>
        <el-form-item label="用户类型" prop="type">
            <el-select v-model="dataForm.type" placeholder="类型">
                <el-option label="普通用户" :value="1"></el-option>
                <el-option label="合伙人" :value="2"></el-option>
            </el-select>
        </el-form-item>
        <el-form-item label="备注" prop="remark">
            <!--                          <el-input v-model="dataForm.remark" placeholder="备注"></el-input>-->
            <el-input type="textarea" rows="3" v-model="dataForm.remark" placeholder="备注"></el-input>
        </el-form-item>


<!--          <el-form-item label="小程序openId" prop="xopenId">-->
<!--          <el-input v-model="dataForm.xopenId" placeholder="小程序openId"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="微信的unionId" prop="unionId">-->
<!--          <el-input v-model="dataForm.unionId" placeholder="微信的unionId"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="是否关注" prop="subscribe">-->
<!--          <el-input v-model="dataForm.subscribe" placeholder="是否关注"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="关注时间" prop="subscribeTime">-->
<!--          <el-input v-model="dataForm.subscribeTime" placeholder="关注时间"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="返回用户关注的渠道来源: ADD_SCENE_SEARCH 公众号搜索，ADD_SCENE_ACCOUNT_MIGRATION 公众号迁移，ADD_SCENE_PROFILE_CARD 名片分享，ADD_SCENE_QR_CODE 扫描二维码，ADD_SCENEPROFILE LINK 图文页内名称点击，ADD_SCENE_PROFILE_ITEM 图文页右上角菜单，ADD_SCENE_PAID 支付后关注，ADD_SCENE_OTHERS 其他" prop="subscribeScene">-->
<!--          <el-input v-model="dataForm.subscribeScene" placeholder="返回用户关注的渠道来源: ADD_SCENE_SEARCH 公众号搜索，ADD_SCENE_ACCOUNT_MIGRATION 公众号迁移，ADD_SCENE_PROFILE_CARD 名片分享，ADD_SCENE_QR_CODE 扫描二维码，ADD_SCENEPROFILE LINK 图文页内名称点击，ADD_SCENE_PROFILE_ITEM 图文页右上角菜单，ADD_SCENE_PAID 支付后关注，ADD_SCENE_OTHERS 其他"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="二维码扫码场景（开发者自定义）" prop="qrScene">-->
<!--          <el-input v-model="dataForm.qrScene" placeholder="二维码扫码场景（开发者自定义）"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="二维码扫码场景描述（开发者自定义）" prop="qrSceneStr">-->
<!--          <el-input v-model="dataForm.qrSceneStr" placeholder="二维码扫码场景描述（开发者自定义）"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="头像图片地址（默认微信）" prop="headImgUrl">-->
<!--          <el-input v-model="dataForm.headImgUrl" placeholder="头像图片地址（默认微信）"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="性别: 1表示男性, 2表示女性" prop="sex">-->
<!--          <el-input v-model="dataForm.sex" placeholder="性别: 1表示男性, 2表示女性"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="性别描述信息: 男、女、未知等" prop="sexDesc">-->
<!--          <el-input v-model="dataForm.sexDesc" placeholder="性别描述信息: 男、女、未知等"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="语言" prop="language">-->
<!--          <el-input v-model="dataForm.language" placeholder="语言"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="省" prop="province">-->
<!--          <el-input v-model="dataForm.province" placeholder="省"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="市" prop="city">-->
<!--          <el-input v-model="dataForm.city" placeholder="市"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="区" prop="country">-->
<!--          <el-input v-model="dataForm.country" placeholder="区"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="用户授权定位的城市(只需要定位到城市即可)" prop="locationCity">-->
<!--          <el-input v-model="dataForm.locationCity" placeholder="用户授权定位的城市(只需要定位到城市即可)"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="创建时间" prop="createTime">-->
<!--          <el-input v-model="dataForm.createTime" placeholder="创建时间"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="一级ID" prop="pid">-->
<!--          <el-input v-model="dataForm.pid" placeholder="一级ID"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="二级ID" prop="pid2">-->
<!--          <el-input v-model="dataForm.pid2" placeholder="二级ID"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="三级ID" prop="pid3">-->
<!--          <el-input v-model="dataForm.pid3" placeholder="三级ID"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="下级数(只有一级)" prop="subs">-->
<!--          <el-input v-model="dataForm.subs" placeholder="下级数(只有一级)"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="下级订单数(只有一级)" prop="subOrders">-->
<!--          <el-input v-model="dataForm.subOrders" placeholder="下级订单数(只有一级)"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="今日新增下级数(只有一级)" prop="todaySubAdds">-->
<!--          <el-input v-model="dataForm.todaySubAdds" placeholder="今日新增下级数(只有一级)"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="今日下级订单数(只有一级)" prop="todaySubOrders">-->
<!--          <el-input v-model="dataForm.todaySubOrders" placeholder="今日下级订单数(只有一级)"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="累计订单数量(自己的)" prop="orders">-->
<!--          <el-input v-model="dataForm.orders" placeholder="累计订单数量(自己的)"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="累计订单金额(自己的)" prop="orderMoneys">-->
<!--          <el-input v-model="dataForm.orderMoneys" placeholder="累计订单金额(自己的)"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="本周累计订单数量(自己的)" prop="weekOrders">-->
<!--          <el-input v-model="dataForm.weekOrders" placeholder="本周累计订单数量(自己的)"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="本周累计订单金额(自己的)" prop="weekOrderMoneys">-->
<!--          <el-input v-model="dataForm.weekOrderMoneys" placeholder="本周累计订单金额(自己的)"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="累计收益" prop="profitTotal">-->
<!--          <el-input v-model="dataForm.profitTotal" placeholder="累计收益"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="累计推广收益" prop="profitSpread">-->
<!--          <el-input v-model="dataForm.profitSpread" placeholder="累计推广收益"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="累计合伙人收益" prop="profitPartner">-->
<!--          <el-input v-model="dataForm.profitPartner" placeholder="累计合伙人收益"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="累计已提现(不显示)" prop="profitWithdraw">-->
<!--          <el-input v-model="dataForm.profitWithdraw" placeholder="累计已提现(不显示)"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="冻结金额(不显示)" prop="frozen">-->
<!--          <el-input v-model="dataForm.frozen" placeholder="冻结金额(不显示)"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="可用金额" prop="balance">-->
<!--          <el-input v-model="dataForm.balance" placeholder="可用金额"></el-input>-->
<!--      </el-form-item>-->
<!--          <el-form-item label="未读消息数量" prop="unReadMsg">-->
<!--          <el-input v-model="dataForm.unReadMsg" placeholder="未读消息数量"></el-input>-->
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
        phone: '',
        status: '',
        type: '',
        nickname: '',
        remark: '',
        openId: '',
        xopenId: '',
        unionId: '',
        subscribe: '',
        subscribeTime: '',
        subscribeScene: '',
        qrScene: '',
        qrSceneStr: '',
        headImgUrl: '',
        sex: '',
        sexDesc: '',
        language: '',
        province: '',
        city: '',
        country: '',
        locationCity: '',
        createTime: '',
        pid: '',
        pid2: '',
        pid3: '',
        subs: '',
        subOrders: '',
        todaySubAdds: '',
        todaySubOrders: '',
        orders: '',
        orderMoneys: '',
        weekOrders: '',
        weekOrderMoneys: '',
        profitTotal: '',
        profitSpread: '',
        profitPartner: '',
        profitWithdraw: '',
        frozen: '',
        balance: '',
        unReadMsg: ''
      }
    }
  },
  computed: {

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
      this.$http.get(`/av/user/${this.dataForm.id}`).then(({ data: res }) => {
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
        this.$http[!this.dataForm.id ? 'post' : 'put']('/av/user/', this.dataForm).then(({ data: res }) => {
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
