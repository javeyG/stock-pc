<template>
  <el-container class="user-center">
    <el-header class="user-header">
      <home-header></home-header>
    </el-header>
    <div class="usercot change">
      <el-container class="main-wrapper">
        <el-aside width="178px">
          <menu-box></menu-box>
        </el-aside>
        <el-main>
          <div class="wrapper">
            <div class="box page-part transaction">
              <div class="box-contain clearfix">
                <div class="text-center">
                  <i style="font-size: 1.5rem;" class="maincolor-font iconfont icon-jiaoyi"></i>
                </div>

                <el-card v-if="$store.state.productSetting.indexDisplay || $store.state.userInfo.accountType === 1"
                         class="box-card" style="margin-top:20px;">
                  <!-- <div slot="header" class="clearfix">
                      <span>融资指数互转</span>
                  </div> -->
                  <div class="text item">
                    <el-tabs v-model="activeName">
                      <el-tab-pane label="融资转指数" name="first">
                        <div class="auth-box">
                          <el-alert
                            center
                            :closable="false"
                            style="width:400px;margin:10px auto;"
                            title="当前融资账户可用余额"
                            type="warning">
                            <span>{{ $store.state.userInfo.enableAmt }}</span>
                          </el-alert>
                          <el-form label-width="100px" v-model="form" ref="ruleForm" class="demo-form-inline">
                            <el-form-item label="转账金额" prop="number">
                              <el-input type='number' @change="getIntNumber()" v-model="form.account1"
                                        placeholder="请输入转账金额">
                                <el-button @click="selectAll1" slot="append">全部</el-button>
                              </el-input>
                              <span class="red">转账金额仅支持整数</span>
                            </el-form-item>
                          </el-form>
                          <div slot="footer" class="dialog-footer">
                            <el-button type="primary" :loading="isloading" @click="Onsubmit(1)">确认转入指数账户</el-button>
                          </div>
                        </div>
                      </el-tab-pane>
                      <el-tab-pane label="指数转融资" name="second">
                        <div class="auth-box">
                          <el-alert
                            center
                            :closable="false"
                            style="width:400px;margin:10px auto;"
                            title="当前指数账户可用余额"
                            type="warning">
                            <span>{{ $store.state.userInfo.enableIndexAmt }}</span>
                          </el-alert>
                          <el-form label-width="100px" v-model="form" ref="ruleForm" class="demo-form-inline">
                            <el-form-item label="转账金额" prop="number">
                              <el-input type='number' v-model="form.account2" @change="getIntNumber2"
                                        placeholder="请输入转账金额">
                                <el-button @click="selectAll2" slot="append">全部</el-button>
                              </el-input>
                              <span class="red">转账金额仅支持整数</span>
                            </el-form-item>
                          </el-form>
                          <div slot="footer" class="dialog-footer">
                            <el-button type="primary" :loading="isloading" @click="Onsubmit(2)">确认转入融资账户</el-button>
                          </div>
                        </div>
                      </el-tab-pane>
                    </el-tabs>

                  </div>
                </el-card>

                <el-card v-if="$store.state.productSetting.futuresDisplay || $store.state.userInfo.accountType === 1"
                         class="box-card" style="margin-top:20px;">
                  <!-- <div slot="header" class="clearfix">
                      <span>指数转融资</span>
                      <span class="red">转账金额仅支持整数</span>
                  </div> -->
                  <div class="text item">
                    <el-tabs v-model="activeName2">
                      <el-tab-pane label="融资转期货" name="first">
                        <div class="auth-box">
                          <el-alert
                            center
                            :closable="false"
                            style="width:400px;margin:10px auto;"
                            title="当前指数账户可用余额"
                            type="warning">
                            <span>{{ $store.state.userInfo.enableAmt }}</span>
                          </el-alert>
                          <el-form label-width="100px" v-model="form" ref="ruleForm" class="demo-form-inline">
                            <el-form-item label="转账金额" prop="number">
                              <el-input type='number' v-model="form.account3" @change="getIntNumber3"
                                        placeholder="请输入转账金额">
                                <el-button @click="selectAll3" slot="append">全部</el-button>
                              </el-input>
                              <span class="red">转账金额仅支持整数</span>
                            </el-form-item>
                          </el-form>

                          <div slot="footer" class="dialog-footer">
                            <el-button type="primary" :loading="isloading" @click="Onsubmit(3)">确认转入期货账户</el-button>
                          </div>
                        </div>
                      </el-tab-pane>
                      <el-tab-pane label="期货转融资" name="second">
                        <div class="auth-box">
                          <el-alert
                            center
                            :closable="false"
                            style="width:400px;margin:10px auto;"
                            title="当前期货账户可用余额"
                            type="warning">
                            <span>{{ $store.state.userInfo.enableFuturesAmt }}</span>
                          </el-alert>
                          <el-form label-width="100px" v-model="form" ref="ruleForm" class="demo-form-inline">
                            <el-form-item label="转账金额" prop="number">
                              <el-input type='number' v-model="form.account4" @change="getIntNumber4"
                                        placeholder="请输入转账金额">
                                <el-button @click="selectAll4" slot="append">全部</el-button>
                              </el-input>
                              <span class="red">转账金额仅支持整数</span>
                            </el-form-item>
                          </el-form>

                          <div slot="footer" class="dialog-footer">
                            <el-button type="primary" :loading="isloading" @click="Onsubmit(4)">确认转入融资账户</el-button>
                          </div>
                        </div>
                      </el-tab-pane>
                    </el-tabs>

                  </div>
                </el-card>

                <el-card v-if="$store.state.userInfo.accountType === 1"
                         class="box-card" style="margin-top:20px;">
                  <div class="text item">
                    <el-tabs v-model="activeName3">
                      <el-tab-pane label="融资转港股" name="first">
                        <div class="auth-box">
                          <el-alert
                            center
                            :closable="false"
                            style="width:400px;margin:10px auto;"
                            title="当前融资账户可用余额"
                            type="warning">
                            <span>{{ $store.state.userInfo.enableAmt }}</span>
                          </el-alert>
                          <el-form label-width="100px" v-model="form" ref="ruleForm" class="demo-form-inline">
                            <el-form-item label="转账金额" prop="number">
                              <el-input type='number' @change="getIntNumber5()" v-model="form.account5"
                                        placeholder="请输入转账金额">
                                <el-button @click="selectAll5" slot="append">全部</el-button>
                              </el-input>
                              <span class="red">转账金额仅支持整数</span>
                            </el-form-item>
                          </el-form>
                          <div slot="footer" class="dialog-footer">
                            <el-button type="primary" :loading="isloading" @click="Onsubmit(5)">确认转入港股账户</el-button>
                          </div>
                        </div>
                      </el-tab-pane>
                      <el-tab-pane label="港股转融资" name="second">
                        <div class="auth-box">
                          <el-alert
                            center
                            :closable="false"
                            style="width:400px;margin:10px auto;"
                            title="当前港股账户可用余额"
                            type="warning">
                            <span>{{ $store.state.userInfo.hkEnableAmt }}</span>
                          </el-alert>
                          <el-form label-width="100px" v-model="form" ref="ruleForm" class="demo-form-inline">
                            <el-form-item label="转账金额" prop="number">
                              <el-input type='number' v-model="form.account6" @change="getIntNumber6"
                                        placeholder="请输入转账金额">
                                <el-button @click="selectAll6" slot="append">全部</el-button>
                              </el-input>
                              <span class="red">转账金额仅支持整数</span>
                            </el-form-item>
                          </el-form>
                          <div slot="footer" class="dialog-footer">
                            <el-button type="primary" :loading="isloading" @click="Onsubmit(6)">确认转入指数账户</el-button>
                          </div>
                        </div>
                      </el-tab-pane>
                    </el-tabs>
                  </div>
                </el-card>

                <el-card v-if="$store.state.userInfo.accountType === 1"
                         class="box-card" style="margin-top:20px;">
                  <div class="text item">
                    <el-tabs v-model="activeName4">
                      <el-tab-pane label="融资转美股" name="first">
                        <div class="auth-box">
                          <el-alert
                            center
                            :closable="false"
                            style="width:400px;margin:10px auto;"
                            title="当前融资账户可用余额"
                            type="warning">
                            <span>{{ $store.state.userInfo.enableAmt }}</span>
                          </el-alert>
                          <el-form label-width="100px" v-model="form" ref="ruleForm" class="demo-form-inline">
                            <el-form-item label="转账金额" prop="number">
                              <el-input type='number' @change="getIntNumber7()" v-model="form.account7"
                                        placeholder="请输入转账金额">
                                <el-button @click="selectAll7" slot="append">全部</el-button>
                              </el-input>
                              <span class="red">转账金额仅支持整数</span>
                            </el-form-item>
                          </el-form>
                          <div slot="footer" class="dialog-footer">
                            <el-button type="primary" :loading="isloading" @click="Onsubmit(7)">确认转入美股账户</el-button>
                          </div>
                        </div>
                      </el-tab-pane>
                      <el-tab-pane label="美股转融资" name="second">
                        <div class="auth-box">
                          <el-alert
                            center
                            :closable="false"
                            style="width:400px;margin:10px auto;"
                            title="当前美股账户可用余额"
                            type="warning">
                            <span>{{ $store.state.userInfo.usEnableAmt }}</span>
                          </el-alert>
                          <el-form label-width="100px" v-model="form" ref="ruleForm" class="demo-form-inline">
                            <el-form-item label="转账金额" prop="number">
                              <el-input type='number' v-model="form.account8" @change="getIntNumber8"
                                        placeholder="请输入转账金额">
                                <el-button @click="selectAll8" slot="append">全部</el-button>
                              </el-input>
                              <span class="red">转账金额仅支持整数</span>
                            </el-form-item>
                          </el-form>
                          <div slot="footer" class="dialog-footer">
                            <el-button type="primary" :loading="isloading" @click="Onsubmit(8)">确认转入指数账户</el-button>
                          </div>
                        </div>
                      </el-tab-pane>
                    </el-tabs>
                  </div>
                </el-card>

                <el-card v-if="$store.state.userInfo.accountType === 1"
                         class="box-card" style="margin-top:20px;">
                  <div class="text item">
                    <el-tabs v-model="activeName5">
                      <el-tab-pane label="融资转北证" name="first">
                        <div class="auth-box">
                          <el-alert
                            center
                            :closable="false"
                            style="width:400px;margin:10px auto;"
                            title="当前融资账户可用余额"
                            type="warning">
                            <span>{{ $store.state.userInfo.enableAmt }}</span>
                          </el-alert>
                          <el-form label-width="100px" v-model="form" ref="ruleForm" class="demo-form-inline">
                            <el-form-item label="转账金额" prop="number">
                              <el-input type='number' @change="getIntNumber9()" v-model="form.account9"
                                        placeholder="请输入转账金额">
                                <el-button @click="selectAll9" slot="append">全部</el-button>
                              </el-input>
                              <span class="red">转账金额仅支持整数</span>
                            </el-form-item>
                          </el-form>
                          <div slot="footer" class="dialog-footer">
                            <el-button type="primary" :loading="isloading" @click="Onsubmit(9)">确认转入北证账户</el-button>
                          </div>
                        </div>
                      </el-tab-pane>
                      <el-tab-pane label="北证转融资" name="second">
                        <div class="auth-box">
                          <el-alert
                            center
                            :closable="false"
                            style="width:400px;margin:10px auto;"
                            title="当前北证账户可用余额"
                            type="warning">
                            <span>{{ $store.state.userInfo.bjEnableAmt }}</span>
                          </el-alert>
                          <el-form label-width="100px" v-model="form" ref="ruleForm" class="demo-form-inline">
                            <el-form-item label="转账金额" prop="number">
                              <el-input type='number' v-model="form.account10" @change="getIntNumber10"
                                        placeholder="请输入转账金额">
                                <el-button @click="selectAll10" slot="append">全部</el-button>
                              </el-input>
                              <span class="red">转账金额仅支持整数</span>
                            </el-form-item>
                          </el-form>
                          <div slot="footer" class="dialog-footer">
                            <el-button type="primary" :loading="isloading" @click="Onsubmit(10)">确认转入指数账户</el-button>
                          </div>
                        </div>
                      </el-tab-pane>
                    </el-tabs>
                  </div>
                </el-card>
              </div>

            </div>

          </div>
          <!-- <home-footer :siteInfo="siteInfo"></home-footer> -->
        </el-main>
      </el-container>
    </div>
  </el-container>

</template>

<script>
  import HomeHeader from '../../../components/HeaderOrder'
  import HomeFooter from '../../../components/Footer'
  import MenuBox from './menu'
  import * as api from '../../../axios/api'

  export default {
    components: {
      HomeHeader,
      HomeFooter,
      MenuBox
    },
    props: {},
    data() {
      return {
        hasAuth: false,
        isloading: false,
        form: {
          account1: '',
          account2: '',
          account3: '',
          account4: '',
          account5: '',
          account6: '',
          account7: '',
          account8: '',
          account9: '',
          account10: '',
          password: ''
        },
        activeName: 'first',
        activeName2: 'first',
        activeName3: 'first',
        activeName4: 'first',
        activeName5: 'first',
      }
    },
    watch: {},
    computed: {},
    created() {
      this.getUserInfo()
    },
    mounted() {
      this.$store.state.userMenu = 'change'
    },
    methods: {
      async getUserInfo() {
        // 获取用户信息
        let data = await api.getUserInfo()
        if (data.status === 0) {
          // 判断是否登录
          this.$store.state.userInfo = data.data
        } else {

        }
      },
      getIntNumber() {
        if (this.form.account1 >= this.$store.state.userInfo.enableAmt) {
          this.form.account1 = this.$store.state.userInfo.enableAmt
        }
        this.form.account1 = Math.floor(this.form.account1)
      },
      getIntNumber2() {
        if (this.form.account2 >= this.$store.state.userInfo.enableIndexAmt) {
          this.form.account2 = this.$store.state.userInfo.enableIndexAmt
        }
        this.form.account2 = Math.floor(this.form.account2)
      },
      getIntNumber3() {
        if (this.form.account3 >= this.$store.state.userInfo.enableAmt) {
          this.form.account3 = this.$store.state.userInfo.enableAmt
        }
        this.form.account3 = Math.floor(this.form.account3)
      },
      getIntNumber4() {
        if (this.form.account4 >= this.$store.state.userInfo.enableFuturesAmt) {
          this.form.account4 = this.$store.state.userInfo.enableFuturesAmt
        }
        this.form.account4 = Math.floor(this.form.account4)
      },
      getIntNumber5() {
        if (this.form.account5 >= this.$store.state.userInfo.enableAmt) {
          this.form.account5 = this.$store.state.userInfo.enableAmt
        }
        this.form.account5 = Math.floor(this.form.account5)
      },
      getIntNumber6() {
        if (this.form.account6 >= this.$store.state.userInfo.hkEnableAmt) {
          this.form.account6 = this.$store.state.userInfo.hkEnableAmt
        }
        this.form.account6 = Math.floor(this.form.account6)
      },
      getIntNumber7() {
        if (this.form.account7 >= this.$store.state.userInfo.enableAmt) {
          this.form.account7 = this.$store.state.userInfo.enableAmt
        }
        this.form.account7 = Math.floor(this.form.account7)
      },
      getIntNumber8() {
        if (this.form.account8 >= this.$store.state.userInfo.usEnableAmt) {
          this.form.account8 = this.$store.state.userInfo.usEnableAmt
        }
        this.form.account8 = Math.floor(this.form.account8)
      },
      getIntNumber9() {
        if (this.form.account9 >= this.$store.state.userInfo.enableAmt) {
          this.form.account9 = this.$store.state.userInfo.enableAmt
        }
        this.form.account9 = Math.floor(this.form.account9)
      },
      getIntNumber10() {
        if (this.form.account10 >= this.$store.state.userInfo.bjEnableAmt) {
          this.form.account10 = this.$store.state.userInfo.bjEnableAmt
        }
        this.form.account10 = Math.floor(this.form.account10)
      },
      selectAll1() {
        // 选择全部
        this.form.account1 = Math.floor(this.$store.state.userInfo.enableAmt)
      },
      selectAll2() {
        // 选择全部
        this.form.account2 = Math.floor(this.$store.state.userInfo.enableIndexAmt)
      },
      selectAll3() {
        // 选择全部
        this.form.account3 = Math.floor(this.$store.state.userInfo.enableAmt)
      },
      selectAll4() {
        // 选择全部
        this.form.account4 = Math.floor(this.$store.state.userInfo.enableFuturesAmt)
      },
      selectAll5() {
        // 选择全部
        this.form.account5 = Math.floor(this.$store.state.userInfo.enableAmt)
      },
      selectAll6() {
        // 选择全部
        this.form.account6 = Math.floor(this.$store.state.userInfo.hkEnableAmt)
      },
      selectAll7() {
        // 选择全部
        this.form.account7 = Math.floor(this.$store.state.userInfo.enableAmt)
      },
      selectAll8() {
        // 选择全部
        this.form.account8 = Math.floor(this.$store.state.userInfo.usEnableAmt)
      },
      selectAll9() {
        // 选择全部
        this.form.account9 = Math.floor(this.$store.state.userInfo.enableAmt)
      },
      selectAll10() {
        // 选择全部
        this.form.account10 = Math.floor(this.$store.state.userInfo.bjEnableAmt)
      },
      async getSettingInfo() {
        // 网站设置信息
        let data = await api.getSetting()
        if (data.status === 0) {
          // 成功
          this.settingInfo = data.data
        } else {
          this.$message.error(data.msg)
        }
      },
      async Onsubmit(type) {
        let types = {
          1: 1,
          2: 2,
          3: 3,
          4: 4,
          5: 7,
          6: 10,
          7: 5,
          8: 8,
          9: 6,
          10: 9,
        }
        // 融资转指数
        let opt = {
          // amt: type === 1 ? this.form.account1 : type === 2 ? this.form.account2 : type === 3 ? this.form.account3 : this.form.account4,
          amt: this.form[`account${type}`],
          type: types[type] // 1 融资转指数 2 指数转融资
        }
        let data = await api.AmtChange(opt)
        if (data.status === 0) {
          this.$message.success(data.msg)
          this.$router.push('/user')
        } else {
          this.$message.error(data.msg)
        }
      }
    }
  }
</script>
<style lang="less" scoped>
  .black-bg .change .el-input-group {
    height: 41px;
  }

  .code {
    font-size: 12px;
    color: #999;
  }

  .auth-box {
    margin-top: 20px;

    .el-row {
      margin-bottom: 10px;
    }
  }

  .authed-box {
    .el-row {
      padding-top: 40px;
      width: 400px;
      margin: 0 auto;
    }
  }

  .wrapper {
    padding: 20px;

    .demo-form-inline {
      // width: 400px;
      margin: 20px auto;
    }

    .auth-box {
      .dialog-footer {
        // width: 400px;
        // margin: 0 auto;

        .el-button {
          margin-left: 100px;
          width: 638px;
          border-radius: 22px;
        }
      }
    }

    .prompt-box {
      padding-bottom: 20px;
      color: #f44336;

      p {
        line-height: 24px;
      }
    }

    .rule-box {
      margin-top: 30px;
      line-height: 26px;
      margin-left: 70px;
    }
  }

  .box-card {
    .red {
      font-size: 12px;
      margin-left: 10px;
    }
  }
</style>
