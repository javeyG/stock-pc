<template>
  <div id="chartInfo" class="wrapper"  style="width: 100%" :style="{background: this.$store.state.systemColor === 'red-bg' ? '#fff' : '#000'}">
    <!-- <div class="header">
      <mt-header :title="detail.name+' ('+detail.code+')'">
        <router-link to="/list" slot="left">
          <mt-button icon="back"></mt-button>
        </router-link>
      </mt-header>
    </div> -->
    <!--<mt-search-->
    <!--style="height:auto;"-->
    <!--fixed-->
    <!--@click.enter.native="toSearch"-->
    <!--placeholder="可输入股票代码或简拼"-->
    <!--&gt;-->
    <!--</mt-search>-->
    <!--<stockHq />-->
    <div class="detail-top">
      <div class="detail-box">
        <a title="添加自选" v-if="!isOptionOpt" href="javascript:;" @click="addOptions"><i class="iconfont icon-wujiaoxing2"></i></a>
        <a title="删除自选" v-if="isOptionOpt" href="javascript:;" @click="deteleOptions"><i class="iconfont icon-wujiaoxing"></i></a>
        <span class="name">{{ detail.name }}</span>
        <span class="code">{{ detail.code }}</span>
      </div>
      <div class="periodWrap">
        <!--      <div class="item" :class="{active: btnIndex == item.minuteIndex}" v-for='item in PeriodForMinuteChart' :key="item.minuteIndex"
                   @click="changeMinuteChartPeroid(item.minuteIndex)">{{
                  item.text
                }}
              </div>-->
        <div class="item" :class="{active: btnIndex == index}" v-for="(period,index) in PeriodListInAlirm" :key="index"
             @click="changeChartPeroid(index)">{{ PeriodMap.get(period) }}
        </div>
      </div>
    </div>
    <!--    <div id="minutechart" ref='minutechart' v-show="Minute.IsShow"></div>-->
    <div id="historychart" ref='historychart' style="width: 200px !important;" v-show="Kline.IsShow"></div>

    <popup v-model="popupVisible" popup-transition="popup-fade" position="bottom">
      <div class="picker-toolbar-title">
        <div @click="popupVisible = !popupVisible">取消</div>
        <div @click="addrConfirm">确定</div>
      </div>
      <picker ref='picker' :slots="slots" value-key='name' @change="onValuesChange"></picker>
    </popup>

  </div>
</template>

<script>
  import {Toast, Popup, Picker} from 'mint-ui'
  import * as api from '@/axios/api'

  import $ from 'jquery'
  import HQChart from 'hqchart'

  let JSCommon = HQChart.Chart
  // let JSCommonStock = HQChart.Stock;
  import 'hqchart/src/jscommon/umychart.resource/css/tools.css'
  import 'hqchart/src/jscommon/umychart.resource/font/iconfont.css'
  import contants from '../../../utils/contans.js'
  import Tools from '../../../services/tools.js'

  JSCommon.JSConsole.Chart.Log = () => {
  }
  JSCommon.JSConsole.Complier.Log = () => {
  }

  function DefaultData() {
  }

  DefaultData.GetKlineOption = function (symbol) {
    let data = {
      Type: '历史K线图',
      Windows: [
        {Index: '均线', Modify: false, Change: false, IsDrawTitleBG: true},
        {Index: 'VOL', Modify: false, Change: false, IsDrawTitleBG: true},
        {Index: 'MACD', Modify: false, Change: false, IsDrawTitleBG: true},
        {Index: 'RSI', Modify: false, Change: false, IsDrawTitleBG: true},
      ], //窗口指标
      Symbol: symbol,
      IsAutoUpdate: true, //是自动更新数据
      AutoUpdateFrequency: 5000,
      IsShowRightMenu: false, //右键菜单

      KLine: {
        DragMode: 1, //拖拽模式 0 禁止拖拽 1 数据拖拽 2 区间选择
        Right: 1, //复权 0 不复权 1 前复权 2 后复权
        Period: 0, //周期 3-年线 4-1分钟 7-30分钟
        MaxReqeustDataCount: 1000, //日线数据最近1000天
        MaxRequestMinuteDayCount: 15,    //分钟数据最近15天
        PageSize: 50, //一屏显示多少数据
        IsShowTooltip: true, //是否显示K线提示信息
        KLineDoubleClick: false, //禁止双击弹框
      },
      IsApiPeriod: true,   //周期数据后台获取
      DragDownload: {
        Day: {Enable: true},
        Minute: {Enable: true},    //开启分钟数据拖拽下载
      },   //拖拽下载

      KLineTitle: //标题设置
        {
          IsShowName: true, //不显示股票名称
          IsShowSettingInfo: false //不显示周期/复权
        },

      Border: //边框
        {
          Left: 0, //左边间距
          Right: 45, //右边间距
          // Top: 20,
          Bottom: 20
        },

      Frame: //子框架设置
        [
          {
            SplitCount: 5,
            SplitType: 1,  //平均分割
            IsShowLeftText: false, 	//不显示左边刻度文字
            IsShowRightText: true,    	//显示右边刻度文字
            Custom:
              [
                {
                  Type: 0,
                  Position: 'left',
                }
              ]
          },
          {SplitCount: 3, StringFormat: 0},
          {SplitCount: 3, StringFormat: 0}
        ],
      ExtendChart:    //扩展图形
        [
          {Name: 'KLineTooltip'}  //手机端tooltip
        ]
    }
    return data
  }

  DefaultData.GetMinuteOption = function (symbol) {
    return {
      Type: '分钟走势图',   //创建图形类型
      //窗口指标
      Windows: [
        {Index: 'MACD', Modify: false, Change: false},
      ],
      Minute: {
        DragMode: 2,
      },
      // Symbol: '600000.sh',
      Symbol: symbol,
      // Symbol: '600000.sh',
      IsAutoUpdate: true,       //是自动更新数据
      DayCount: 1,                     //1 最新交易日数据 >1 多日走势图
      IsShowCorssCursorInfo: true,    //是否显示十字光标的刻度信息
      IsShowRightMenu: true,       //是否显示右键菜单

      MinuteLine: {
        // IsDrawAreaPrice:true,      //是否画价格面积图
      },
      //边框
      Border: {
        Left: 50,         //左边间距
        Right: 50,       //右边间距
        Bottom: 25,      //底部间距
        Top: 25          //顶部间距
      },
      //子框架设置
      Frame: [
        {SplitCount: 5, StringFormat: 0},
        {SplitCount: 3, StringFormat: 0},
        {SplitCount: 3, StringFormat: 0},
      ],
      //扩展图形
      ExtendChart: [
        {Name: 'MinuteTooltip'}  //手机端tooltip
      ],

      // Overlay:    //叠加股票 目前只支持1只股票
      // [
      //     //{Symbol:'600000.sh'}
      // ]
    }

  }

  export default {
    components: {
      Popup,
      Picker
    },
    props: {
      code: {
        type: String,
        default() {

        }
      },
      detailInfo: {
        type: Object,
        default() {

        }
      }
    },
    data() {
      return {

        Kline: {
          IsShow: true,
          Option: null,
          JSChart: null
        },
        Minute: {
          IsShow: false,
          Option: null,
          JSChart: null
        },
        MinuteLine: null,    //外部缓存的走势图数据
        Symbol: '600000.sh',
        //周期取15S(可能有),1M,5M,10M,15M,30M,1H,2H,4H,D(W周,M月可能有)。
        // PeriodListInAlirm: ['D','W','M','','1M','5M','15M','30M','1H'], //没有年周期
        PeriodForMinuteChart: [
          {
            minuteIndex: '分1',
            text: '分时'
          },
          {
            minuteIndex: '分5',
            text: '五日'
          }
        ],
        PeriodListInAlirm: ['D', 'W', 'M', '1M', '5M', '15M', '30M', '1H'],
        PeriodMap: new Map([
          ['D', '日线'],
          ['W', '周线'],
          ['M', '月线'],
          ['1M', '1分钟'],
          ['5M', '5分钟'],
          ['15M', '15分钟'],
          ['30M', '30分钟'],
          ['1H', '60分钟'],
        ]),
        btnIndex: 0,
        AryWindowBorder: null,
        DivWindowBG: null,
        DivKLine: null,
        SelectedFrameID: -1,
        PageIndex: {
          'D': 1,
          'W': 1,
          'M': 1,
          '1M': 1,
          '5M': 1,
          '15M': 1,
          '30M': 1,
          '1H': 1
        },
        isHkSymbol: false,
        // minuteKlinePageIndex: 1,
        // isAutofocus: false,
        historyKlinePageHost: 'https://alirmcom2.market.alicloudapi.com/query/comkmv2', //历史数据-按分页
        stInfoHost: 'https://alirmcom2.market.alicloudapi.com/query/com',
        historyKlineHost: 'https://alirmcom2.market.alicloudapi.com/query/comkm4v2', //历史数据
        historyKlineStartEndHost: 'https://alirmcom2.market.alicloudapi.com/query/comkm2v2', //历史数据-拖拽
        lastestKlineHost: 'https://alirmcom2.market.alicloudapi.com/query/comlstkmv2', //最新柱 - 轮训增量
        popupVisible: false,
        slots: [{
          defaultIndex: 2,

          values: [
            {name: 'KDJ', id: 'KDJ',},
            {name: 'MACD', id: 'MACD',},
            {name: 'RSI', id: 'RSI',},
            {name: 'BOLL', id: 'BOLL',},
            {name: 'VOL', id: 'VOL',},
            {name: '均线', id: '均线',},
          ],
        }],
        marketList: [],

        detail: {}, // 详情
        isOptionOpt: false, // 是否已经添加自选
        timer: null,
        loading: false,
      }
    },
    async created() {
      // 获取指数列表
      let opt = {
        pageNum: this.pageNum,
        pageSize: 15
      }
      let data = await api.getListMarket(opt)
      if (data.status === 0) {
        data.data.forEach(element => {
          this.marketList.push(element)
        })
      }
      console.log('this.$route.query.gid',this.$route.query.gid)
      this.getStockByCode(this.$route.query.gid)

      // this.timer = setInterval(this.refreshList, 5000)
    },
    beforeDestroy() {
      clearInterval(this.timer)
    },
    mounted() {
      // this.getDetail()
      if (this.$store.state.userInfo.phone) {
        // 判断是否登录
        this.getOpation()
      } else {
        // 获取用户信息
        this.getUserInfo()
      }

      this.OnSize()
      window.onresize = this.throttle(this.OnSize)

      // this.CreateKLineChart()  //创建K线图
      // this.CreateMinuteChart(1)  //创建K线图
    },
    methods: {
      async getStockByCode(code) {
        // if (Tools.getURLParams('symbol')) this.Symbol = Tools.getURLParams('symbol')
        this.detail.code = code
        if (code) {
          let flag = this.marketList.findIndex(item => item.indexGid === code)
          if (flag === -1) this.detail.code = this.formatCode(code)
          this.Symbol = `${this.$route.query.gid}`.toUpperCase()
          console.log(this.Symbol)
          await this.getDetail()
          await this.CreateKLineChart()  //创建K线图
        }
      },
      throttle(fn) {
        let startTime = new Date().getTime()
        return function () {
          let endTime = new Date().getTime()
          if (endTime - startTime > 1000) {
            fn()
            startTime = new Date().getTime()
          }
        }
      },
      OnSize() {
        // let width = window.innerWidth
        let width = document.getElementById("chartInfo").offsetWidth
        let chartHeight = window.innerHeight - 400
        console.log(`width:${width},height:${chartHeight}`)
        this.$refs.historychart.style.width = width + 'px'
        this.$refs.historychart.style.height = chartHeight + 'px'

        // this.$refs.minutechart.style.width = width + 'px'
        // this.$refs.minutechart.style.height = chartHeight + 'px'

        if (this.Kline.JSChart != null && this.Kline.IsShow) this.Kline.JSChart.OnSize()
        // if (this.Minute.JSChart != null && this.Minute.IsShow) this.Minute.JSChart.OnSize()
      },
      /*QueryLastTradeDate(date) {
        var url = urlObj.apiLastTradeDate
        var data = {
          "date": date,
          "isPrevTradeDate": "true"
        }
        urlObj.post(url, data, this.LastTradeDateCallback)
      },
      LastTradeDateCallback(res) {
        if (res.code != 0) {
          console.log(res.message)
          // this.$message.error(res.message);
          return
        }
        let lastTradeDate = res.prevdate
        let lastTradeDateText = StringFormat.StockStringFormat.FormatDateString(res.prevdate)
        console.log(`lastTradeDateText=${lastTradeDateText}`)
      },*/
      changeMinuteChartPeroid(minuteIndex) {
        if (this.btnIndex.toString().indexOf('分') === -1) {
          this.Minute.IsShow = true
          this.Kline.IsShow = false
        }
        this.btnIndex = minuteIndex

        const day = parseInt(minuteIndex.split('')[1])
        console.log(`day:${day}`)
        if (!this.Minute.JSChart) {
          this.CreateMinuteChart(day)
          return
        }

        this.Minute.JSChart.ChangeDayCount(day)
      },
      changeChartPeroid(index) {
        if (this.btnIndex.toString().indexOf('分') > -1) {
          this.Minute.IsShow = false
          this.Kline.IsShow = true
        }
        this.btnIndex = index
        if (index >= 3) {
          index += 1
        }
        this.Kline.JSChart.ChangePeriod(index)
      },
      //创建K线图
      CreateKLineChart() {
        if (this.Kline.JSChart) return
        console.log(111111)
        // var blackStyle = JSCommon.HQChartStyle.GetStyleConfig(JSCommon.STYLE_TYPE_ID.BLACK_ID);
        // blackStyle.CorssCursorVPenColor="rgba(250,250,250,0.8)";	//修改垂直线颜色
        // JSCommon.JSChart.SetStyle(blackStyle);

        let resource = JSCommon.JSChart.GetResource() //获取hqchart资源配置
        // resource.FrameLogo.Text = null
        // resource.IndexTitleBGColor='rgb(255,255,255)'; //修改指标标题背景色 注：修改颜色配置必须在初始化前
        // resource.DefaultTextColor='rgb(255,0,0)'; //修改指标标题背景色 注：修改颜色配置必须在初始化前

        this.Kline.Option = DefaultData.GetKlineOption(this.Symbol)
        this.Kline.Option.NetworkFilter = this.NetworkFilter
        this.DivKLine = this.$refs.historychart
        let chart = JSCommon.JSChart.Init(this.$refs.historychart)
        chart.SetOption(this.Kline.Option)
        chart.AddEventCallback({
          event: JSCommon.JSCHART_EVENT_ID.ON_CLICK_INDEXTITLE,
          callback: this.OnClickIndexTitle
        })//点击事件通知回调
        // console.log(this.Kline.Option,"this.Kline.Option::::")
        this.Kline.JSChart = chart
        console.log(this.Kline)
      },
      OnClickIndexTitle(event, data, obj) {
        if (data.FrameID === 0) {
          this.slots[0].values = [
            {name: 'BOLL', id: 'BOLL',},
            {name: 'MA', id: 'MA',}
          ]

        } else {
          this.slots[0].values = [
            {name: 'KDJ', id: 'KDJ',},
            {name: 'MACD', id: 'MACD',},
            {name: 'RSI', id: 'RSI',},
            {name: 'VOL', id: 'VOL',},
          ]
        }
        this.slots[0].defaultIndex = this.slots[0].values.findIndex(item => data.Title.indexOf(item.name) > -1)
        this.popupVisible = true
        this.FrameID = data.FrameID
      },
      CreateMinuteChart(day) {
        if (this.Minute.JSChart) return
        this.OnSize()
        console.log('CreateMinuteChart')
        this.Minute.Option = DefaultData.GetMinuteOption(this.Symbol)
        this.Minute.Option.DayCount = day
        this.Minute.Option.NetworkFilter = this.NetworkFilter
        let chart = JSCommon.JSChart.Init(this.$refs.minutechart)
        chart.SetOption(this.Minute.Option)
        // console.log(this.Minute.Option,"this.Minute.Option::::")
        this.Minute.JSChart = chart
      },
      formatSymbolFun(symbol) {
        symbol = symbol.toUpperCase()
        if (symbol.indexOf('.SH') > -1) {
          return 'SH' + symbol.split('.SH')[0]
        } else if (symbol.indexOf('.SZ') > -1) {
          return 'SZ' + symbol.split('.SZ')[0]
        } else if (symbol.indexOf('.HK') > -1) {
          return 'HK' + symbol.split('.HK')[0]
        } else {
          return symbol
        }
      },
      // 格式化code
      formatCode(symbol) {
        symbol = symbol.toUpperCase()
        if (symbol.indexOf('SH') > -1) {
          return symbol.split('SH')[1]
        } else if (symbol.indexOf('SZ') > -1) {
          return symbol.split('SZ')[1]
        } else if (symbol.indexOf('HK') > -1) {
          return symbol.split('HK')[1]
        } else {
          return symbol
        }
      },
      //获取股票基本信息
      getStockInfo(formatSymbol) {
        return new Promise((resolve, reject) => {
          $.ajax({
            type: 'get',
            url: `${this.stInfoHost}?symbol=${formatSymbol}&withks=1&withticks=1`,
            // url: `${this.stInfoHost}?symbol=sh600000&withks=1&withticks=1`,
            headers: {
              'Authorization': `APPCODE ${contants.appcode}`
            },
            success: function (res) {
              resolve(res)
            },
            error: function (error) {
              reject(error)
            }
          })
        })
      },
      queryKlineData(url, callback, stInfo, symbol, klineCallback) { //klineCallback对应networkfilter里的callback
        $.ajax({
          type: 'get',
          url,
          headers: {
            'Authorization': `APPCODE ${contants.appcode}`
          },
          success: function (res) {
            callback(res, stInfo, symbol, klineCallback)
          },
          error: function (error) {
            console.log('error:', error)
          }
        })
      },
      getDateForDayLine(strSecond) {
        const date = new Date(parseInt(strSecond) * 1000)
        const year = date.getFullYear()
        const month = date.getMonth() + 1
        const day = date.getDate()
        const result = '' + year + ((month >= 10) ? month : '0' + month) + ((day >= 10) ? day : '0' + day)
        return result
      },
      getToday(noSymbol) {
        const date = new Date()
        const year = date.getFullYear()
        const month = date.getMonth() + 1
        const day = date.getDate()
        if (!noSymbol) {
          return '' + year + '-' + ((month >= 10) ? month : '0' + month) + '-' + ((day >= 10) ? day : '0' + day)
        } else {
          return '' + year + ((month >= 10) ? month : '0' + month) + ((day >= 10) ? day : '0' + day)
        }
      },
      RecvKlineData(res, stInfo, symbol, callback) {
        const name = stInfo.N
        if (!(JSON.parse(res).Obj)) return
        const dataAry = JSON.parse(res).Obj.split(';').reverse()
        const stockData = dataAry.reduce(
          (target, value) => {
            const itemAry = value.split(',')
            const targetItem = []
            targetItem.push(Number(this.getDateForDayLine(itemAry[0]))) //date（日期）
            targetItem.push(0)  //yclose（前收盘价）
            targetItem.push(Number(itemAry[2]))  // open（开盘价）
            targetItem.push(Number(itemAry[3])) //high（最高）
            targetItem.push(Number(itemAry[4])) //low （最低）
            targetItem.push(Number(itemAry[1])) // close（收盘价）
            targetItem.push(Number(itemAry[6])) // vol（成交量）
            targetItem.push(Number(itemAry[5])) //amount（成交金额）
            target.push(targetItem)
            return target
          },
          []
        )
        // console.log('stockData::',stockData);
        //设置前收盘
        stockData[0][1] = stockData[0][5]
        for (let i = 0; i < stockData.length - 1; ++i) {
          if (i < (stockData.length - 1)) {
            let before = stockData[i]
            let after = stockData[i + 1]
            after[1] = before[5]
          }
        }
        let hqChartData = {
          code: 0,
          count: stockData.length,
          name,
          symbol,
          data: stockData
        }
        console.log('日线接口数据：', hqChartData)
        callback(hqChartData)
      },
      GetKlineData(data, callback) {
        data.PreventDefault = true	//设置hqchart不请求数据

        var symbol = data.Request.Data.symbol
        var period = data.Self.Period
        // console.log('GetKlineData', period);
        // var right=data.Self.Right;
        // var dayCount=data.Self.MaxReqeustDataCount;

        // const formatSymbol = this.formatSymbolFun(symbol)
        const formatSymbol = symbol
        const periodInAlirm = this.getQueryPeriod(period)
        const today = this.getToday()

        this.getStockInfo(formatSymbol)
          .then(
            value => {
              const data = JSON.parse(value).Obj

              const pageIndex = this.PageIndex[periodInAlirm]
              const psize = this.getPSizeByPeroidPageIndex(periodInAlirm, pageIndex)
              this.queryKlineData(
                `${this.historyKlinePageHost}?period=${periodInAlirm}&pidx=1&psize=${psize}&symbol=${formatSymbol}&withlast=1`,
                this.RecvKlineData,
                data,
                symbol,
                callback
              )

              this.queryKlineData(
                `${this.historyKlineHost}?date=${today}&period=${periodInAlirm}&symbol=${formatSymbol}&withlast=1`,
                this.RecvKlineData,
                data,
                symbol,
                callback
              )
            }
          )
          .catch(error => {
            console.log(error)
          })
      },
      getTimeForDayLine(strSecond, symbol) { //symbol，不传返回时分秒，传入hhmm,返回时分
        const date = new Date(parseInt(strSecond) * 1000)
        const hour = date.getHours()
        const minute = date.getMinutes()
        const second = date.getSeconds()
        if (symbol === 'hhmm') {
          return '' + (hour >= 10 ? hour : '0' + hour) + (minute >= 10 ? minute : '0' + minute)
        } else {
          return '' + (hour >= 10 ? hour : '0' + hour) + (minute >= 10 ? minute : '0' + minute) + (second >= 10 ? second : '0' + second)
        }
      },
      GetRealKlineData(data, callback) {
        data.PreventDefault = true	//设置hqchart不请求数据

        var symbol = data.Request.Data.symbol[0]
        var period = data.Self.Period
        // var right=data.Self.Right;
        // var dayCount=data.Self.MaxReqeustDataCount;

        // const formatSymbol = this.formatSymbolFun(symbol)
        const formatSymbol = symbol
        const periodInAlirm = this.getQueryPeriod(period)
        const SecondTick = parseInt(new Date().getTime() / 1000)
        // console.log(`请求时间戳：${SecondTick}`);

        // this.getStockInfo(formatSymbol) //暂时去掉，耗流量
        // .then(
        //   value => {
        //     const data = JSON.parse(value).Obj
        //     this.queryKlineData(
        //       `${this.lastestKlineHost}?fromtick=${SecondTick}&period=${periodInAlirm}&symbol=${formatSymbol}`,
        //       this.RecvRealKlineData,
        //       data,
        //       symbol,
        //       callback
        //     )
        //   }
        // )
        // .catch(error => {
        //   console.log(error);
        // })
      },
      GetFlowCapitalData(data, callback) {
        data.PreventDefault = true
      },
      RecvRealKlineData(res, stInfo, symbol, callback) {
        const name = stInfo.N
        const price = stInfo.P
        const yClose = stInfo.YC
        const dataAry = JSON.parse(res).Obj.split(';')
        // console.log('最新柱返回数据：', stockData1);
        const stockData = dataAry[0].split(',')
        const hqData = {
          'stock': [],
          'start': 0,
          'end': 0,
          'count': 1,
          'ticket': 0,
          'version': 'HQ.Stock 2.0',
          'code': 0
        }
        const stockObj = {
          'time': Number(this.getTimeForDayLine(stockData[0])),
          'date': Number(this.getDateForDayLine(stockData[0])),
          price,
          'open': Number(stockData[2]),
          'yclose': yClose,
          'high': Number(stockData[3]),
          'low': Number(stockData[4]),
          'vol': Number(stockData[6]),
          'amount': Number(stockData[5]),
          'symbol': symbol,
          name
        }
        console.log('最新日线数据：', stockObj)
        hqData.stock.push(stockObj)
        callback(hqData)
      },
      getQueryPeriod(period) {
        if (period === 3) {
          return 0
        } else if (period < 3) {
          return this.PeriodListInAlirm[period]
        } else {
          return this.PeriodListInAlirm[period - 1]
        }
      },
      RecvDragDayData(res, stInfo, symbol, callback) {
        if (!(JSON.parse(res).Obj)) return
        const dataAry = JSON.parse(res).Obj.split(';').reverse()
        const stockData = dataAry.reduce(
          (target, value) => {
            const itemAry = value.split(',')
            const targetItem = []
            targetItem.push(Number(this.getDateForDayLine(itemAry[0]))) //date（日期）
            targetItem.push(0)  //yclose（前收盘价）
            targetItem.push(Number(itemAry[2]))  // open（开盘价）
            targetItem.push(Number(itemAry[3])) //high（最高）
            targetItem.push(Number(itemAry[4])) //low （最低）
            targetItem.push(Number(itemAry[1])) // close（收盘价）
            targetItem.push(Number(itemAry[6])) // vol（成交量）
            targetItem.push(Number(itemAry[5])) //amount（成交金额）
            target.push(targetItem)
            return target
          },
          []
        )
        // console.log('stockData::',stockData);
        //设置前收盘
        stockData[0][1] = stockData[0][5]
        for (let i = 0; i < stockData.length - 1; ++i) {
          if (i < (stockData.length - 1)) {
            let before = stockData[i]
            let after = stockData[i + 1]
            after[1] = before[5]
          }
        }
        let hqChartData = {
          code: 0,
          data: stockData
        }
        // console.log('日线接口数据：',hqChartData);
        callback(hqChartData)
      },
      GetDragDayData(data, callback) {
        data.PreventDefault = true
        var symbol = data.Request.Data.symbol
        var period = data.Self.Period    //周期
        var startDate = data.Request.Data.first.date.toString()
        console.log('开始日期：', startDate)

        // const formatSymbol = this.formatSymbolFun(symbol)
        const formatSymbol = symbol
        const periodInAlirm = this.getQueryPeriod(period)

        this.getStockInfo(formatSymbol)
          .then(
            value => {
              const data = JSON.parse(value).Obj

              let dateed = encodeURIComponent(Tools.FormatDateString(startDate))
              const num = -100 //前100天
              let datest = encodeURIComponent(Tools.date_getPointDate(startDate + '', num, '-'))
              const pageIndex = ++(this.PageIndex[periodInAlirm])
              const psize = this.getPSizeByPeroidPageIndex(periodInAlirm, pageIndex)
              if (psize > 0) {
                this.queryKlineData(
                  `${this.historyKlinePageHost}?period=${periodInAlirm}&pidx=${pageIndex}&psize=${psize}&symbol=${formatSymbol}&withlast=0`,
                  this.RecvDragDayData,
                  data,
                  symbol,
                  callback
                )
              }

              this.queryKlineData(
                `${this.historyKlineStartEndHost}?dateed=${dateed}&datest=${datest}&period=${periodInAlirm}&symbol=${formatSymbol}&withlast=0`,
                this.RecvDragDayData,
                data,
                symbol,
                callback
              )
            }
          )
          .catch(error => {
            console.log(error)
          })

      },
      RecvDragMinuteData(res, stInfo, symbol, callback) {
        const name = stInfo.N
        if (!(JSON.parse(res).Obj)) return
        const dataAry = JSON.parse(res).Obj.split(';').reverse()
        // console.log('ajax res:', dataAry);
        const stockData = dataAry.reduce(
          (target, value) => {
            const itemAry = value.split(',')
            const targetItem = []
            targetItem.push(Number(this.getDateForDayLine(itemAry[0]))) //date（日期）
            targetItem.push(0)  //yclose（前收盘价）
            targetItem.push(Number(itemAry[2]))  // open（开盘价）
            targetItem.push(Number(itemAry[3])) //high（最高）
            targetItem.push(Number(itemAry[4])) //low （最低）
            targetItem.push(Number(itemAry[1])) // close（收盘价）
            targetItem.push(Number(itemAry[6])) // vol（成交量）
            targetItem.push(Number(itemAry[5])) //amount（成交金额）
            targetItem.push(Number(this.getTimeForDayLine(itemAry[0], 'hhmm'))) //时间格式 hhmm

            target.push(targetItem)
            return target
          },
          []
        )
        console.log('更多分钟k线:stockData::', stockData)
        //设置前收盘
        stockData[0][1] = stockData[0][5]
        for (let i = 0; i < stockData.length - 1; ++i) {
          if (i < (stockData.length - 1)) {
            let before = stockData[i]
            let after = stockData[i + 1]
            after[1] = before[5]
          }
        }
        let hqChartData = {
          name,
          symbol,
          data: stockData,
          code: 0,
          version: '2.0'
        }
        callback(hqChartData)
      },
      RecvRealMinuteData(res, stInfo, symbol, callback) {
        const name = stInfo.N
        const yClose = stInfo.YC
        const dataAry = JSON.parse(res).Obj.split(';')
        const stockData = dataAry[0].split(',')
        const hqData = {
          data: [],
          ver: 2,
          code: 0,
          symbol,
          name
        }
        const newData = []
        newData.push(Number(this.getDateForDayLine(stockData[0]))) //0=日期
        newData.push(yClose) //1=前收盘
        newData.push(Number(stockData[2])) //2=开盘价
        newData.push(Number(stockData[3])) //3=最高
        newData.push(Number(stockData[4])) //4= 最低
        newData.push(Number(stockData[1])) //5=收盘
        newData.push(Number(stockData[6])) //6=成交量
        newData.push(Number(stockData[5])) //7=成交金额
        newData.push(Number(this.getTimeForDayLine(stockData[0], 'hhmm'))) //8=时间格式
        hqData.data.push(newData)
        callback(hqData)

      },
      RecvMinuteHistoryData(res, stInfo, symbol, callback) {
        const name = stInfo.N
        if (!(JSON.parse(res).Obj)) return
        const dataAry = JSON.parse(res).Obj.split(';').reverse()
        const stockData = dataAry.reduce(
          (target, value) => {
            const itemAry = value.split(',')
            const targetItem = []
            targetItem.push(Number(this.getDateForDayLine(itemAry[0]))) //date（日期）
            targetItem.push(0)  //yclose（前收盘价）
            targetItem.push(Number(itemAry[2]))  // open（开盘价）
            targetItem.push(Number(itemAry[3])) //high（最高）
            targetItem.push(Number(itemAry[4])) //low （最低）
            targetItem.push(Number(itemAry[1])) // close（收盘价）
            targetItem.push(Number(itemAry[6])) // vol（成交量）
            targetItem.push(Number(itemAry[5])) //amount（成交金额）
            targetItem.push(Number(this.getTimeForDayLine(itemAry[0], 'hhmm'))) //分钟周期需要的时间格式：hhmm
            target.push(targetItem)
            return target
          },
          []
        )

        //设置前收盘
        stockData[0][1] = stockData[0][5]
        for (let i = 0; i < stockData.length - 1; ++i) {
          if (i < (stockData.length - 1)) {
            let before = stockData[i]
            let after = stockData[i + 1]
            after[1] = before[5]
          }
        }
        const hqData = {
          code: 0,
          count: stockData.length,
          name,
          symbol,
          data: stockData
        }
        callback(hqData)
      },
      GetHistoryMinuteData(data, callback) {
        data.PreventDefault = true
        var symbol = data.Request.Data.symbol
        var period = data.Self.Period
        console.log(period)
        // var right=data.Self.Right;
        // var dayCount=data.Self.MaxRequestMinuteDayCount;

        // const formatSymbol = this.formatSymbolFun(symbol)
        const formatSymbol = symbol
        const periodInAlirm = this.getQueryPeriod(period)
        const today = this.getToday()
        // const startDate = Tools.date_getPointDate(today, -1, '-')

        // var log=`[${this.GetDateTime()}][KLineChart::RequestHistoryMinuteData] 全量分钟K数据请求信息 代码=${symbol} 周期=${period} 复权=${right} K线(天数)=${dayCount}`;
        // console.log(log);

        this.getStockInfo(formatSymbol)
          .then(
            value => {
              const data = JSON.parse(value).Obj

              this.queryKlineData(
                `${this.historyKlinePageHost}?period=${periodInAlirm}&pidx=1&psize=500&symbol=${formatSymbol}&withlast=1`,
                this.RecvMinuteHistoryData,
                data,
                symbol,
                callback
              )
            }
          )
          .catch(error => {
            console.log(error)
          })
      },
      GetRealMinuteData(data, callback) {
        data.PreventDefault = true	//设置hqchart不请求数据

        var symbol = data.Request.Data.symbol[0]
        var period = data.Self.Period
        // var right=data.Self.Right;
        // var dayCount=data.Self.MaxReqeustDataCount;

        // const formatSymbol = this.formatSymbolFun(symbol)
        const formatSymbol = symbol
        const periodInAlirm = this.getQueryPeriod(period)
        const SecondTick = parseInt(new Date().getTime() / 1000)

        // this.getStockInfo(formatSymbol)  //暂时去掉，耗流量
        // .then(
        //   value => {
        //     const data = JSON.parse(value).Obj

        //     this.queryKlineData(
        //       `${this.lastestKlineHost}?fromtick=${SecondTick}&period=${periodInAlirm}&symbol=${formatSymbol}`,
        //       this.RecvRealMinuteData,
        //       data,
        //       symbol,
        //       callback
        //     )

        //   }
        // )
        // .catch(error => {
        //   console.log(error);
        // })
      },
      getTimeForDragMinuteLine(time, needSecond) { //time-分钟时间 hhmm,needSecond,接口如果需要秒
        time = parseInt(time)
        const hour = parseInt(time / 100) < 10 ? '0' + parseInt(time / 100) : '' + parseInt(time / 100)
        const minute = parseInt(time % 100) < 10 ? '0' + parseInt(time % 100) : '' + parseInt(time % 100)
        if (needSecond) {
          return hour + ':' + minute + ':00'
        } else {
          return hour + ':' + minute
        }
      },
      getDragMinuteData(data, callback) {
        data.PreventDefault = true
        var symbol = data.Request.Data.symbol
        var period = data.Self.Period    //周期
        var endDateKline = data.Request.Data.first.date.toString()
        var endTimeKline = data.Request.Data.first.time.toString()
        console.log('分钟拖拽-当前日期和时间：', endDateKline, endTimeKline)

        // const formatSymbol = this.formatSymbolFun(symbol)
        const formatSymbol = symbol
        const periodInAlirm = this.getQueryPeriod(period)

        this.getStockInfo(formatSymbol)
          .then(
            value => {
              const data = JSON.parse(value).Obj

              // const num = -1 //接口输出的日周期数据只有最近3年，1分钟周期数据只有最近15天,5分钟最多30天,60分最多180天,(更多历史数据可申请打包下载)。
              // let datest = Tools.date_getPointDate(endDateKline, num, '-') + ' 09:30'
              // let dateed = Tools.date_getPointDate(endDateKline, num, '-') + ' 15:00'

              // console.log(`分钟拖拽：开始时间:${datest}，结束时间:${dateed}`);
              const pageIndex = ++(this.PageIndex[periodInAlirm])
              const psize = this.getPSizeByPeroidPageIndex(periodInAlirm, pageIndex)
              if (psize > 0) {
                this.queryKlineData(
                  `${this.historyKlinePageHost}?period=${periodInAlirm}&pidx=${pageIndex}&psize=${psize}&symbol=${formatSymbol}&withlast=0`,
                  this.RecvDragMinuteData,
                  data,
                  symbol,
                  callback
                )
              }
            }
          )
          .catch(error => {
            console.log(error)
          })
      },
      getPSizeByPeroidPageIndex(periodInAlirm, pageIndex) {
        /*
        periodInAlirm： 是当前页加1后的值
        接口输出的日周期数据只有最近3年,周周期数据只有最近10年,日周期数据只有最近10年
        日周期：365*3=1095 1095/500=2余95，如果页数是3，则psize为95，大于3，则psize为0
        周周期：48*10=480
        月周期：12*10=120

        1分钟周期数据只有最近15天,  240*15=3600 3600/500=7余100 ，如果页数是8，则psize为100，大于8，则psize为0
        5分钟最多30天,  48*30=1440 1440/500=2余440，如果页数是3，则psize为440，大于3，则psize为0
        猜想：15分钟最多60天,  16*60=960 960/500=1余460，如果页数是2，则psize为460，大于2，则psize为0
        猜想：30分钟最多90天,  8*90=720 720/500=1余220，如果页数是2，则psize为220，大于2，则psize为0
        60分最多180天,  4*180=720 720/500=1余220，如果页数是2，则psize为220，大于2，则psize为0
        */
        let psize = 500
        switch (periodInAlirm) {
          case 'D':
            if (pageIndex < 3) {

            } else if (pageIndex == 3) {
              psize = 95
            } else {
              psize = 0
            }
            break
          case 'W':
            if (pageIndex > 1) {
              psize = 0
            } else {
              psize = 480
            }
            break
          case 'M':
            if (pageIndex > 1) {
              psize = 0
            } else {
              psize = 120
            }
            break
          case '1M':
            if (pageIndex < 8) {

            } else if (pageIndex == 8) {
              psize = 100
            } else {
              psize = 0
            }
            break
          case '5M':
            if (pageIndex < 3) {

            } else if (pageIndex == 3) {
              psize = 440
            } else {
              psize = 0
            }
            break
          case '15M':
            if (pageIndex < 2) {

            } else if (pageIndex == 2) {
              psize = 460
            } else {
              psize = 0
            }
            break
          case '30M':
            if (pageIndex < 2) {

            } else if (pageIndex == 2) {
              psize = 220
            } else {
              psize = 0
            }
            break
          case '1H':
            if (pageIndex < 2) {

            } else if (pageIndex == 2) {
              psize = 220
            } else {
              psize = 0
            }
            break
        }
        return psize
      },
      RecvMinuteData(res, stInfo, symbol, callback) {
        const name = stInfo.N
        const price = stInfo.P
        // 昨天收盘价
        const ycPrice = stInfo.YC
        const dataAry = JSON.parse(res).Obj.split(';')
        const stockData = dataAry.reduce(
          (target, value) => {
            const itemAry = value.split(',')
            const targetItem = {
              price: Number(itemAry[1]),
              open: Number(itemAry[2]),
              high: Number(itemAry[3]),
              low: Number(itemAry[4]),
              vol: Number(itemAry[6] * 100),
              amount: Number(itemAry[5]),
              time: Number(this.getTimeForDayLine(itemAry[0], 'hhmm')),
              date: Number(this.getDateForDayLine(itemAry[0])), //不是hqchart的必须字段
              avprice: Number((itemAry[5] / (itemAry[6] * 100)).toFixed(3)),
              // avprice:0,
              // increase: 0,
              // risefall: 0,
            }
            if (targetItem.time !== 1300) target.push(targetItem)
            return target
          },
          []
        )
        const hqchartData = {
          code: 0,
          'stock': [
            {
              'time': stockData[stockData.length - 1].time, //最新的交易时间
              'date': stockData[stockData.length - 1].date, //交易日期
              'price': stockData[stockData.length - 1].price, //最新价/收盘价
              'open': stockData[stockData.length - 1].open, //当日开盘价
              // "yclose": stockData[stockData.length - 1].yclose,
              yclose: ycPrice, //不是hqchart的必须字段
              'high': stockData[stockData.length - 1].high, //当日最高价
              'low': stockData[stockData.length - 1].low, //当日最低价
              'vol': stockData[stockData.length - 1].vol, //当日成交量
              'amount': stockData[stockData.length - 1].amount, //当日成交金额
              symbol,
              // symbol: '600000.sh',
              name,
              'minute': [ //分钟数据数组 1分钟一条数据 ,对应 this.MinuteLine
                ...stockData
              ]
            }
          ]
        }
        console.log('接收分时 hqchartData::', hqchartData)
        callback(hqchartData)
      },
      getMinuteData(data, callback) {
        data.PreventDefault = true	//设置hqchart不请求数据
        var symbol = data.Request.Data.symbol[0]

        // const formatSymbol = this.formatSymbolFun(symbol)
        const formatSymbol = symbol
        const today = this.getToday()

        this.getStockInfo(formatSymbol)
          .then(
            value => {
              const data = JSON.parse(value).Obj

              this.queryKlineData(
                `${this.historyKlineHost}?date=${today}&period=1M&symbol=${formatSymbol}&withlast=1`,
                // `${this.historyKlineHost}?date=${today}&period=1M&symbol=sh600000&withlast=1`,
                this.RecvMinuteData,
                data,
                symbol,
                callback
              )
            }
          )
          .catch(error => {
            console.log(error)
          })
      },
      //网络协议回调
      NetworkFilter(data, callback) {
        console.log(data)
        switch (data.Name) {
          case 'KLineChartContainer::RequestHistoryData':
            this.GetKlineData(data, callback)
            break
          case 'KLineChartContainer::RequestRealtimeData':
            this.GetRealKlineData(data, callback)
            break
          case 'KLineChartContainer::RequestFlowCapitalData':
            this.GetFlowCapitalData(data, callback)
            break
          case 'KLineChartContainer::RequestDragDayData': //日K线拖拽自动下载历史数据
            this.GetDragDayData(data, callback)
            break

          case 'KLineChartContainer::ReqeustHistoryMinuteData':
            this.GetHistoryMinuteData(data, callback)
            break
          case 'KLineChartContainer::RequestMinuteRealtimeData':
            this.GetRealMinuteData(data, callback)
            break
          case 'KLineChartContainer::RequestDragMinuteData': //分钟K线拖拽自动下载历史数据
            this.getDragMinuteData(data, callback)
            break

          //最新分时图
          case 'MinuteChartContainer::RequestMinuteData':
            this.getMinuteData(data, callback)
            break
          // 历史分时图
          case 'MinuteChartContainer::RequestHistoryMinuteData':
            this.GetHistoryMinuteData(data, callback)
            break

          // case "JSSymbolData::GetSymbolPeriodData":  //跨周期数据
          //   GetSymbolPeriodData(data, callback);
          //   break;
          // case "JSSymbolData::GetFinanceData": //财务数据
          //   GetFinanceData(data, callback);
          //   break;
        }
      },

      // 两融交易
      handleTwoBuyClick() {
        this.$router.push({
          path: '/twoBuy',
          query: {
            code: this.detail.code
          }
        })
      },
      // 分仓交易
      handleSubBuyClick() {
        this.$router.push({
          path: '/subWarehouseBuy',
          query: {
            code: this.detail.code
          }
        })
      },
      toSearch() {
        this.$router.push('/searchlist')
      },
      async getUserInfo() {
        // 获取用户信息
        let data = await api.getUserInfo()
        if (data.status === 0) {
          this.$store.state.userInfo = data.data
          this.getOpation()
        } else {
          Toast(data.msg)
        }
        this.$store.state.user = this.user
      },
      async refreshList() {
        if (this.loading) {
          return
        }
        // this.getDetail()
      },
      async getOpation() {
        let opts = {
          code: this.$route.query.code,
        }
        let data = await api.isOption(opts)
        if (data.status === 0) {
          // 0 --> 未添加
          this.isOptionOpt = false
        } else {
          this.isOptionOpt = true
        }
      },
      async getDetail() {
        let opts = {
          // code: this.$route.query.code,
          code: this.detail.code,
        }
        let data = await api.getSingleStock(opts)
        this.loading = false
        if (data.status === 0) {
          this.detail = data.data
          console.log('data.data',data.data)
          console.log('detail',this.detail)
        } else {
          Toast(data.msg)
        }
      },
      async addOptions() {
        //   if(!this.$store.state.userInfo.phone){
        //     MessageBox.confirm('您还未登录，是否去登录?').then(action => {
        //         this.$router.push('/login')
        //     });
        //     return
        //   }
        let data = await api.addOption({code: this.detail.code})
        if (data.status === 0) {
          Toast('添加自选成功')
          this.isOptionOpt = true
        } else {
          Toast(data.msg)
        }
      },
      async deteleOptions() {
        let data = await api.delOption({code: this.detail.code})
        if (data.status === 0) {
          Toast('删除自选股成功')
          this.isOptionOpt = false
        } else {
          Toast(data.msg)
        }
      },

      onValuesChange(picker, values) {
        this.curLine = values[0].name
        console.log(this.curLine, '--->this.curLine')
      },
      addrConfirm() {
        this.Kline.JSChart.ChangeIndex(this.FrameID, this.curLine)
        this.popupVisible = false
      }
    },
  }
</script>
<style lang="less" scoped>
  .wrapper {
    width: 100%;
    // background-color: #151515;

    .detail-top {
      display: flex;
      justify-content: space-between;
      align-items: center;
      .detail-box {
        height: 40px;
        line-height: 40px;

        .name {
          color: #efbb53;
          font-size: 16px;
          margin-left: 8px;
        }

        .code {
          color: #9e9e9e;
          margin-left: 6px;
        }
      }
      .periodWrap {
        height: 35px;
        display: flex;
        .item {
          padding: 0 5px;
          height: 100%;
          text-align: center;
          line-height: 35px;
          flex: 1;
          cursor: pointer;

          &.active {
            color: #efbb53;
          }
        }
      }
    }

    #historychart {
      position: relative;
    }

  }

  .mint-popup {
    width: 100%;
    background: #fff;

    .picker-toolbar-title {
      padding: 10px;
      display: flex;
      justify-content: space-between;
      color: #333;
      font-size: 16px;
    }
  }

</style>
