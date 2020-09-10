<template>

    <el-card shadow="never" class="aui-card--fill">


        <div class="mod-av__user}">
            <el-form :inline="true" :model="dataForm">
                <el-form-item>
                    <el-input v-model="dataForm.kw" placeholder="按昵称和手机号查询" clearable></el-input>
                </el-form-item>
<!--                <el-form-item>-->
<!--                    <el-input v-model="dataForm.balance" placeholder="可用金额大于" clearable></el-input>-->
<!--                </el-form-item>-->
                <el-form-item>
                    可用金额大于:
                    <el-input v-model="dataForm.balance" placeholder="可用金额大于" clearable></el-input>
                </el-form-item>
                <el-form-item>
                    用户类型:
                    <el-select v-model="dataForm.type" placeholder="用户类型" clearable>
                        <el-option label="全部" value=""></el-option>
                        <el-option label="普通用户" value="1"></el-option>
                        <el-option label="合伙人" value="2"></el-option>
                    </el-select>
                </el-form-item>
                <el-form-item>
                    认证状态:
                    <el-select v-model="dataForm.authStatus" placeholder="认证状态" clearable>
                        <el-option label="全部" value=""></el-option>
                        <el-option label="未认证" value="1"></el-option>
                        <el-option label="已认证" value="2"></el-option>
                    </el-select>
                </el-form-item>
                <br/>
                <el-form-item>
                    创建时间:
                    <el-date-picker
                            v-model="createTime"
                            type="daterange"
                            range-separator="-"
                            start-placeholder="开始日期"
                            end-placeholder="结束日期">
                    </el-date-picker>
                </el-form-item>
                <el-form-item>
                    <el-button @click="getDataList2()">{{ $t('query') }}</el-button>
                </el-form-item>
                <el-form-item>
                  <el-button type="info" @click="exportHandle()">{{ $t('export') }}</el-button>
                </el-form-item>
                <!--        <el-form-item>-->
                <!--          <el-button v-if="$hasPermission('av:user:save')" type="primary" @click="addOrUpdateHandle()">{{ $t('add') }}</el-button>-->
                <!--        </el-form-item>-->
                <!--        <el-form-item>-->
                <!--          <el-button v-if="$hasPermission('av:user:delete')" type="danger" @click="deleteHandle()">{{ $t('deleteBatch') }}</el-button>-->
                <!--        </el-form-item>-->
            </el-form>
            <el-table v-loading="dataListLoading" :data="dataList" border
                      @selection-change="dataListSelectionChangeHandle" style="width: 100%;"
                      @sort-change="dataListSortChangeHandle">
                <!--                <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>-->
                <!--                <el-table-column prop="id" label="ID" header-align="center" align="center"></el-table-column>-->
                <el-table-column  width="140" label="昵称" header-align="center" align="center" fixed>
                    <template slot-scope="scope">
                        <el-button type="text"  @click="showUser(scope.row.id)" class="orderNoShow">{{scope.row.nickname}}</el-button>
                    </template>
                </el-table-column>
                <el-table-column label="头像" header-align="center" align="center" width="140">
                    <template slot-scope="scope">
                        <img :src="scope.row.headImgUrl" width="40" height="40" class="head_pic"/>
                    </template>
                </el-table-column>
                <el-table-column prop="phone" label="手机号码" header-align="center" align="center" width="140"></el-table-column>
                <!--        <el-table-column prop="status" label="用户状态" header-align="center" align="center"></el-table-column>-->
                <el-table-column prop="sex" label="性别" header-align="center" :formatter="formatSex"
                                 align="center" width="140"></el-table-column>
                <el-table-column prop="type" label="用户类型" :formatter="formatType" header-align="center"
                                 align="center" width="140"></el-table-column>
                <!--        <el-table-column prop="remark" label="备注" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="openId" label="公众号openId" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="xopenId" label="小程序openId" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="unionId" label="微信的unionId" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="subscribe" label="是否关注" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="subscribeTime" label="关注时间" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="subscribeScene" label="渠道来源" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="qrScene" label="二维码扫码场景（开发者自定义）" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="qrSceneStr" label="二维码扫码场景描述（开发者自定义）" header-align="center" align="center"></el-table-column>-->

                <!--        <el-table-column prop="sexDesc" label="性别描述信息: 男、女、未知等" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="language" label="语言" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="province" label="省" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="city" label="市" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="country" label="区" header-align="center" align="center"></el-table-column>-->
                <el-table-column prop="locationCity" label="城市" header-align="center" align="center" width="140"></el-table-column>
                <el-table-column prop="orders" label="购买数量" sortable="custom" header-align="center"
                                 align="center" width="140"></el-table-column>
                <el-table-column prop="profitTotal" label="累计收益" :formatter="accumulatedEarnings" header-align="center"
                                 align="center" width="140"></el-table-column>
                <el-table-column prop="balance" label="可用金额" :formatter="amountAvailable" header-align="center"
                                 align="center" width="140"></el-table-column>
                <el-table-column prop="profitWithdraw" label="累计提现" :formatter="accumulativeWithdrawal" header-align="center"
                                 align="center" width="140"></el-table-column>
                <el-table-column prop="authStatus" label="认证状态" :formatter="certification" header-align="center"
                                 align="center" width="140">
                    <template slot-scope="scope">
                        <el-tag v-if="scope.row.authStatus === 1" size="small" type="danger">未认证</el-tag>
                        <el-tag v-if="scope.row.authStatus === 2" size="small" type="success">已认证</el-tag>
                    </template>
                </el-table-column>
                <el-table-column prop="remark" label="备注" header-align="center" align="center" width="160"></el-table-column>
                <el-table-column prop="createTime" width="160" sortable="custom" label="创建时间" header-align="center"
                                 align="center"></el-table-column>
                <!--        <el-table-column prop="pid" label="一级ID" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="pid2" label="二级ID" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="pid3" label="三级ID" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="subs" label="下级数(只有一级)" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="subOrders" label="下级订单数(只有一级)" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="todaySubAdds" label="今日新增下级数(只有一级)" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="todaySubOrders" label="今日下级订单数(只有一级)" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="orders" label="累计订单数量(自己的)" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="orderMoneys" label="累计订单金额(自己的)" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="weekOrders" label="本周累计订单数量(自己的)" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="weekOrderMoneys" label="本周累计订单金额(自己的)" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="profitTotal" label="累计收益" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="profitSpread" label="累计推广收益" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="profitPartner" label="累计合伙人收益" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="profitWithdraw" label="累计已提现(不显示)" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="frozen" label="冻结金额(不显示)" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="balance" label="可用金额" header-align="center" align="center"></el-table-column>-->
                <!--        <el-table-column prop="unReadMsg" label="未读消息数量" header-align="center" align="center"></el-table-column>-->
                <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="120">
                    <template slot-scope="scope">
                        <el-dropdown @command="handleCommand">
                            <el-button type="primary" size="small">
                                更多<i class="el-icon-arrow-down el-icon--right"></i>
                            </el-button>
                            <el-dropdown-menu slot="dropdown">
                                <el-dropdown-item :command="{type:1,id:scope.row.id}" v-if="$hasPermission('av:order:orderRecord')">订单记录</el-dropdown-item>
                                <el-dropdown-item :command="{type:2,id:scope.row.id}" v-if="$hasPermission('av:order:orderRecord')">赠送记录</el-dropdown-item>
                                <el-dropdown-item :command="{type:3,id:scope.row.id}" v-if="$hasPermission('av:user:lookSuperior')">查看上级</el-dropdown-item>
                                <el-dropdown-item :command="{type:4,id:scope.row.id}" v-if="$hasPermission('av:course:givingCourses')">赠送课程</el-dropdown-item>
                                <el-dropdown-item :command="{type:5,id:scope.row.id}" >收益明细</el-dropdown-item>
                                <el-dropdown-item v-if="scope.row.type === 2" :command="{type:6,id:scope.row.id}">
                                    合伙人收益
                                </el-dropdown-item>
                                <el-dropdown-item v-if="$hasPermission('av:user:update')"  :command="{type:7,id:scope.row.id}" >
<!--                                    <el-button id="updateUser"  type="text" size="medium " style="color: #606266;" @click="addOrUpdateHandle(scope.row.id)">修改用户</el-button>-->
                                    修改用户
                                </el-dropdown-item>

                            </el-dropdown-menu>
                        </el-dropdown>
                        <!--            <template solt-scope="scope">-->
<!--                                      <el-button v-if="$hasPermission('av:user:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">{{ $t('update') }}</el-button>-->
                        <!--&lt;!&ndash;              <el-button v-if="$hasPermission('av:user:delete')" type="text" size="small" @click="deleteHandle(scope.row.id)">{{ $t('delete') }}</el-button>&ndash;&gt;-->
                        <!--            </template>-->
                    </template>
                </el-table-column>
            </el-table>
            <el-pagination
                    :current-page="page"
                    :page-sizes="[10, 20, 50, 100]"
                    :page-size="limit"
                    :total="total"
                    layout="total, sizes, prev, pager, next, jumper"
                    @size-change="pageSizeChangeHandle"
                    @current-change="pageCurrentChangeHandle">
            </el-pagination>
            <!-- 弹窗 订单记录、赠送记录 -->
            <UserList v-if="userListVisible" ref="userList"></UserList>
            <!-- 赠送课程-->
            <ChooseCourse v-if="chooseCourseVisible" ref="chooseCourse"></ChooseCourse>
            <!--查看上级-->
            <UserSuperior v-if="userSuperiorVisible" ref="userSuperior"></UserSuperior>
            <!--合伙人收益-->
            <UserPartnerIncome v-if="userPartnerIncomeVisible" ref="userPartnerIncome"></UserPartnerIncome>
            <!--修改备注-->
            <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
            <!--查看用户 -->
            <user-view v-if="userViewVisible" ref="userView"></user-view>
        </div>
    </el-card>
</template>
<style>
    .el-input{
        width: 204px
    }
    .el-dropdown-link {
        cursor: pointer;
        color: #409EFF;
    }

    .el-icon-arrow-down {
        font-size: 12px;
    }
    #updateUser:hover{

    }
</style>

<script>
    import mixinViewModule from '@/mixins/view-module'
    import UserList from './user-list'
    import ChooseCourse from "./choose-course"
    import UserSuperior from "./user-superior";
    import UserPartnerIncome from "./user-partner-income";
    import AddOrUpdate from './user-add-or-update'
    import UserView from './user-view'


    export default {
        mixins: [mixinViewModule],
        data() {
            return {
                mixinViewModuleOptions: {
                    getDataListURL: '/av/user/page',
                    getDataListIsPage: true,
                    exportURL: '/av/user/export',
                    deleteURL: '/av/user',
                    deleteIsBatch: true
                },
                createTime: '',
                dataForm: {
                    id: '',
                    kw: '',
                    orders: '',
                    type: '',
                    startTime: '',
                    endTime: '',
                    authStatus: '',
                    balance: ''
                },
                userListVisible: false,
                chooseCourseVisible: false,
                userSuperiorVisible: false,
                userPartnerIncomeVisible: false,
                addOrUpdateVisible: false,
                userViewVisible: false,
            }
        },
        components: {
            ChooseCourse,
            UserList,
            UserSuperior,
            UserPartnerIncome,
            AddOrUpdate,
            UserView
        },
        methods: {
            getDataList2() {
                this.page =1
                if(this.createTime && this.createTime.length > 0){
                    this.dataForm.startTime = this.$moment(this.createTime[0]).format('YYYY-MM-DD');
                    this.dataForm.endTime = this.$moment(this.createTime[1]).format('YYYY-MM-DD');
                }
                this.getDataList();
            },

            showUser(id) {
                this.userViewVisible = true
                this.$nextTick(() => {
                    this.$refs.userView.dataForm.id = id
                    this.$refs.userView.init()
                })
            },
            handleCommand(command) {
                if (command != null) {
                    if (command.type === 4) {
                        this.chooseCourseVisible = true
                    }
                    if (command.type === 3) {
                        this.userSuperiorVisible = true
                    }
                    if (command.type === 6 || command.type === 5) {
                        this.userPartnerIncomeVisible = true
                    }
                    if (command.type === 1 || command.type === 2) {
                        this.userListVisible = true
                    }
                    if (command.type === 7) {
                        // this.addOrUpdateVisible = true
                        this.addOrUpdateHandle(command.id)
                    }
                    this.$nextTick(() => {
                        if (command.type === 4) {
                            this.$refs.chooseCourse.uid = command.id
                            this.$refs.chooseCourse.init()
                        }
                        if (command.type === 3) {
                            this.$refs.userSuperior.dataForm.id = command.id
                            this.$refs.userSuperior.init()
                        }
                        if (command.type === 5 || command.type === 6) {
                            this.$refs.userPartnerIncome.id = command.id;
                            this.$refs.userPartnerIncome.type = command.type;
                            this.$refs.userPartnerIncome.init()
                        }
                        if (command.type === 1 || command.type === 2) {
                            this.$refs.userList.userId = command.id;
                            this.$refs.userList.type = command.type;
                            this.$refs.userList.init()
                        }
                        if (command.type === 7) {
                            this.$refs.addOrUpdate.id = command.id;
                            this.$refs.addOrUpdate.init()
                        }
                    })

                }
            },

            //用户类型: 1普通用户, 2合伙人
            formatType(row) {
                switch (row.type) {
                    case 1 :
                        return '普通用户'
                    case 2 :
                        return '合伙人'
                    default :
                        return '未知'
                }
            },
            //性别: 1表示男性, 2表示女性
            formatSex(row) {
                switch (row.sex) {
                    case 1 :
                        return '男性'
                    case 2 :
                        return '女性'
                    default :
                        return '未知'
                }
            },
            //认证状态：1未认证 2已认证
            certification(row, column) {
                if (row.authStatus === null) {
                    return ''
                }
                if (row.authStatus === 1) {
                    return '未认证'
                }
                if (row.authStatus === 2) {
                    return '已认证'
                }
            },
            accumulatedEarnings(row, column) {
                if (row.profitTotal == null){
                    return 0.00
                }
                return row.profitTotal.toFixed(2)
            },
            amountAvailable(row,column) {
                if (row.balance == null) {
                    return 0.00
                }
                return row.balance.toFixed(2)
            },
            accumulativeWithdrawal(row,column) {
                if (row.profitWithdraw == null) {
                    return 0.00
                }
                return row.profitWithdraw.toFixed(2)
            }
        }
    }
</script>
