<template>
  <el-container class="product">
    <el-header>
      <home-header></home-header>
    </el-header>
    <!-- <el-container class=""> -->
        <backdrop1>
          <slot>
            <div class="bancot">
              <div class="description">
                <div class="tit"><span class="zh">产品说明</span><span class="en">PRODUCT DESCRIPTION</span></div>
                <div class="text">我们最大的资产是我们的员工，{{siteInfo.siteName}}只聘用那些富有经验的专业人员，他们已经在金融服务业展示了他们一流的客户服务技巧和领带能力。提供专业的客户服务。我们的销售和营运代表将以专业的中文对客户的任何询问进行解答。</div>
              </div>
            </div>
          </slot>
        </backdrop1>
        <div class="main ">
          <div class="cot ">
            <div class="notice">
              <layout>
              <div slot="left">
                <div class="s_left" >
                  <div class="not_l" style="width: 35px;overflow: hidden;"><span class="iconfont icon-kaihuhedui"></span>开户须知</div>
                  <div class="not_r">
                    <ul>
                      <li>开设真实账户（注册会员）之前一定要认真阅读和查看本公司的《交易规则》和《风险揭示与告知》。</li>
                      <li>客户具备的开户条件：年满十八周岁、具有完全民事行为能力的公民。客户须以真实的、合法的身份开户并保证资金来源的合法性。</li>
                      <li>客户自备用于相互转账的银行卡（开通网上银行），客户应妥善保管好银行密码及交易密码，并定期更换密码，不得向任何人泄露密码。</li>
                      <li>自行注册，并遵循《一人一个账号规则》。</li>
                      <li>正式账户充值成功后即可开始交易。</li>
                    </ul>
                  </div>
                </div>
              </div>
              <div slot="right">
                <div class="s_right">
                  <div class="not_l" style="width: 35px;overflow: hidden;"><span class="iconfont icon-kaihuhedui"></span>操盘须知</div>
                    <div class="not_r">
                    <ul>
                      <li>您投资的 本金 最低 1 元;</li>
                      <li>申请资金后，随时买进卖出;</li>
                      <li>账户匹配后，当天即可交易;</li>
                      <li>当资金接近亏损强平预警线时，需尽快补足保证金；如未及时补仓，可能会被风控系统强平;</li>
                      <li>当操盘资金低于平仓线下时，我们有权将您的账户实行止损操作;</li>
                      <li>单只股票持仓不得超过账户总资金的 90.00% ;</li>                           
                    </ul>
                    </div>
                  </div>
                </div>
              </layout>
            </div>
            <!-- 股市 -->
            <div class="stock">
              <div class="tit"><span class="iconfont icon-_gushiloushi"></span> 沪深股市</div>
              <div class="info">
                <div class="left">
                  <div class="item">
                    <div class="i_tit">标的名称：</div>
                    <div class="msg">上海、深圳股市融资融券板块</div>
                  </div>
                  <div class="item">
                    <div class="i_tit">市场交易时间：	</div>
                    <div class="msg">
                      <span>第一节：{{sitesetInfo.transAmBegin}}-{{sitesetInfo.transAmEnd}}</span>
                      <span>第二节：{{sitesetInfo.transPmBegin}}-{{sitesetInfo.transPmEnd}}</span>
                      <p>（北京时间）(实际开放交易时间以交易规则为准）</p>
                    </div>
                  </div>
                  <div class="item">
                    <div class="i_tit">最小交易单位：	</div>
                    <div class="msg">{{(sitesetInfo.buyMinAmt/100).toFixed(2)}}手 ，每1手=100股</div>
                  </div>
                  <div class="item">
                    <div class="i_tit">最大交易单位：	</div>
                    <div class="msg">{{(sitesetInfo.buyMaxNum/100).toFixed(2)}}手 ，每1手=100股</div>
                  </div>
                  <div class="item">
                    <div class="i_tit">交易方式：	</div>
                    <div class="msg">可买多、卖空，买升、买趺、当日买入，当日平仓，賺取当日波段振幅价差！</div>
                  </div>
                </div>
                <div class="mid">
                  <div class="item">
                    <div class="i_tit">交易额度：		</div>
                    <div class="msg">采用融资、融券方式，买涨买跌方式买进卖出</div>
                  </div>
                  <div class="item">
                    <div class="i_tit">最小购买金额：		</div>
                    <div class="msg">{{sitesetInfo.buyMinAmt}} 元</div>
                  </div>
                  <div class="item">
                    <div class="i_tit">手续费：		</div>
                    <div class="msg">买入及卖出时收取，依交易总价 * {{sitesetInfo.buyFee}} 收取</div>
                  </div>
                  <div class="item">
                    <div class="i_tit">印花税：		</div>
                    <div class="msg">买入时收取，依交易总价 * {{sitesetInfo.dutyFee}} 收取</div>
                  </div>
                  <div class="item">
                    <div class="i_tit">留仓费：		</div>
                    <div class="msg">如该交易未於收盘前平仓，则该交易於收盘后记为留仓单，收取该股收盘价 * 留仓股数 * {{sitesetInfo.stayFee}} 留仓费，（节假日亦收取）</div>
                  </div>
                </div>
                <div class="right">
                  <div class="item">
                    <div class="i_tit">最大留仓天数：		</div>
                    <div class="msg">{{sitesetInfo.stayMaxDays}} 天</div>
                  </div>
                  <div class="item">
                    <div class="i_tit">出金规则：		</div>
                    <div class="msg">当前有持仓无法出金，最小出金金额为 {{sitesetInfo.withMinAmt}} 元</div>
                  </div>
                  <div class="item">
                    <div class="i_tit">出金时间：			</div>
                    <div class="msg">出金时间为 {{sitesetInfo.withTimeBegin}} :00 和 {{sitesetInfo.withTimeEnd}} : 00 之间</div>
                  </div>
                  <div class="item">
                    <div class="i_tit">时差：		</div>
                    <div class="msg">夏令：0小时；冬令：0小时</div>
                  </div>
                  <div class="item">
                    <div class="i_tit">数据来源：		</div>
                    <div class="msg">中国上海证券交易所及深训证券交易所</div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
    <newFooter />
  </el-container>
</template>

<script>
  import HomeHeader from '../../components/HeaderOrder'
  import newFooter from '../../components/newFooter'
  // import TableBox from './components/table'
  import * as api from '../../axios/api'
  import backdrop1 from '../../components/backdrop1'
  import layout from '../../components/layout'

  export default {
    components: {
      HomeHeader,
      newFooter,
      backdrop1,
      layout
    },
    props: {},
    data () {
      return {
        siteInfo: {}, // 站点信息
        sitesetInfo: {
          transAmBegin: '',
          transAmEnd: '',
          transPmBegin: '',
          transPmEnd: '',
          buyminnum: '',
          buymaxnum: '',
          buyminamt: '',
          buyfee: '',
          dutyfee: '',
          stayfee: '',
          staymaxday: '',
          begintime: '',
          endtime: '',
          withminamt: ''
        }, // 设置信息
        timer: null
      }
    },
    watch: {},
    computed: {},
    created () {
      // this.timer = setInterval(this.refreshList, 5000)
    },
    beforeDestroy () {
      clearInterval(this.timer)
    },
    mounted () {
      this.getSettingSite()
      this.getInfoSite()
      this.$store.state.activeIndex = 'product'
    },
    methods: {
      async getInfoSite () {
        // 获取站点详情
        let result = await api.getInfoSite()
        if (result.status === 0) {
          this.siteInfo = result.data
        } else {
          this.$message.error(result.msg)
        }
      },
      async getSettingSite () {
        // 获取站点设置信息
        let result = await api.getSetting()
        if (result.status === 0) {
          this.sitesetInfo = result.data
        } else {
          this.$message.error(result.msg)
        }
      }
    }
  }
</script>
<style lang="less" scoped>
  .introduce {
    padding: 20px;
    min-height: 400px;

    .title {
      font-size: 20px;
      line-height: 38px;
    }

    .introduce-body {
      line-height: 28px;
      padding-top: 30px;
      text-indent: 2em;
      font-size: 14px;
    }
  }

  .table-box {
    .table-title {
      padding: 0;
      color: #cbd3ff;
      width: 894px;
    }
  }

  .panel-box {
    margin: 0 auto;
    margin-top: 35px;
    width: 900px;
    box-shadow: 0.8px 0.6px 15px rgba(0, 0, 0, 0.1);

    .panel-title {
      padding: 6px 0;
      // background: #24252c;
    }

    .panel-body {
      padding: 15px 0;
      // background: #1f2026;
    }
  }
</style>
