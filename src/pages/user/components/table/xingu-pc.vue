<template>
  <el-container class="user-center">
    <el-header class="user-header">
      <home-header></home-header>
    </el-header>
    <div class="usercot message">
      <el-container class="main-wrapper">
        <el-aside width="178px">
          <menu-box></menu-box>
        </el-aside>
        <el-main>
          <div class="con-box account futuresholdposition">
            <el-tabs v-model="activeName" class="aaaa">
              <el-tab-pane label="新股配筹" name="list">
                <div class="user-info">
                  <el-table :data="dataList" style="width: 100%">

                    <el-table-column prop="names" label="股票名称">
                      <template slot-scope="scope">
                        <span style="color: #fff">{{ scope.row.name }}</span>
                      </template>
                    </el-table-column>
                    <el-table-column prop="code" label="申购代码">
                      <template slot-scope="scope">
                        <span style="color: #fff">{{ scope.row.code }}</span>
                      </template>
                    </el-table-column>
                    <el-table-column prop="price" label="发行价格">
                      <template slot-scope="scope">
                        <span style="color: #fff">{{ scope.row.price ? Number(scope.row.price).toFixed(2) : '-' }}/股</span>
                      </template>
                    </el-table-column>
                    <el-table-column prop="price" label="配筹价格">
                      <template slot-scope="scope">
                        <span style="color: #fff">{{ scope.row.price ? Number(scope.row.price).toFixed(2) : '-' }}</span>
                      </template>
                    </el-table-column>
                    <el-table-column
                      fixed="right"
                      prop="isLock"
                      width="120px"
                      label="操作"
                    >
                      <template slot-scope="scope">
                        <el-button
                          type="success"
                          plain
                          size="small"
                          @click="showModel(scope.row)"
                        >申购
                        </el-button
                        >
                      </template>
                    </el-table-column>
                  </el-table>
                </div>
              </el-tab-pane>
              <el-tab-pane label="申购记录" name="list1">
                <div class="user-info">
                  <el-table :data="dataList1" style="width: 100%">
                    <el-table-column
                      prop="newName"
                      label="股票名称"
                    >
                    </el-table-column>
                    <el-table-column
                      prop="newCode"
                      label="股票代码"
                    >
                    </el-table-column>
                    <el-table-column
                      prop="applyNums"
                      label="申购数量"
                    >
                    </el-table-column>
                    <el-table-column
                      prop="buyPrice"
                      label="申购单价"
                    >
                    </el-table-column>
                    <el-table-column
                      prop="bond"
                      label="申购价格"
                    >
                    </el-table-column>
                    <el-table-column prop="orderNo" label="申购配号">
                    </el-table-column>
                    <el-table-column prop="status" label="申购状态">
                      <template slot-scope="scope">
                        <el-tag :type="statusTypes[scope.row.status].type"><span>{{ statusTypes[scope.row.status].text }}</span></el-tag>
                      </template>
                      <template slot-scope="scope">
                        <p class="bounceIn">
                          <span :class="statusTypes[scope.row.status].type">{{ statusTypes[scope.row.status].text }}</span>
                        </p>
                      </template>
                    </el-table-column>
                    <el-table-column
                      prop="addTime"
                      label="申购时间"
                    >
                    </el-table-column>
                  </el-table>
                </div>
              </el-tab-pane>
              <el-tab-pane label="中签记录" name="list2">
                <div class="user-info">
                  <el-table :data="dataList2" style="width: 100%">
                    <el-table-column
                      prop="newName"
                      label="股票名称"
                    >
                    </el-table-column>
                    <el-table-column
                      prop="newCode"
                      label="股票代码"
                    >
                    </el-table-column>
                    <el-table-column
                      prop="applyNums"
                      label="申购数量"
                    >
                    </el-table-column>
                    <el-table-column
                      prop="buyPrice"
                      label="申购单价"
                    >
                    </el-table-column>
                    <el-table-column
                      prop="bond"
                      label="申购价格"
                    >
                    </el-table-column>
                    <el-table-column prop="orderNo" label="申购配号">
                    </el-table-column>
                    <el-table-column
                      prop="addTime"
                      label="申购时间"
                    >
                    </el-table-column>
                  </el-table>
                </div>
              </el-tab-pane>
            </el-tabs>
          </div>
          <el-dialog
            show-close
            :visible.sync="buyPopupVisible"
            width="30%"
          >
            <div class="storeinformation_popup">
              <el-form :model="form" ref="form" class="demo-form">
                <div class="storeinformation_popup_top">
                  <el-form-item>
                    <el-input placeholder="请输入数量" v-model="form.buyNums">
                      <template slot="append">手</template>
                    </el-input>
                  </el-form-item>
                </div>
                <el-form-item style="text-align:center;">
                  <el-button @click="buyPopupVisible = false">取 消</el-button>
                  <el-button
                    type="primary"
                    @click="buyStock()"
                    style="background-color: #fff !important;color:#333 !important;border-color:#DCDFE6 !important;"
                  >确 定
                  </el-button>
                </el-form-item>
              </el-form>
            </div>
          </el-dialog>
        </el-main>
      </el-container>
    </div>
  </el-container>
</template>

<script>
  import HomeHeader from "../../../../components/HeaderOrder"
  import HomeFooter from "../../../../components/Footer"
  import MenuBox from "../menu"
  import * as api from "../../../../axios/api"

  export default {
    components: {
      HomeHeader,
      HomeFooter,
      MenuBox,
    },
    props: {},
    data() {
      return {
        statusTypes: {
          1: {
            text: '待中签',
            type: 'red'
          },
          2: {
            text: '未中签',
            type: 'red'
          },
          3: {
            text: '已中签',
            type: 'green'
          },
          4: {
            text: '已缴纳',
            type: 'green'
          },
          5: {
            text: '已转持仓',
            type: 'green'
          },
        },
        pageNum: 1,
        pageSize: 11,
        activeName: "list",
        buyPopupVisible: false,
        detail: {},
        // 购买表单
        form: {
          buyNums: '',
          password: '',
        },
        dataList: [],
        dataList1: [],
        dataList2: [],
      }
    },
    computed: {},
    created() {
    },
    mounted() {
      this.$store.state.userMenu = "2-16"
      this.getlist()
      this.getlist1()
      this.getlist2()
    },
    watch: {
      activeName(val, oldval) {
        if (val === 'list1') {
          this.getlist1()
        } else if (val === 'list2') {
          this.getlist2()
        }
      },
    },
    methods: {
      async getlist() {
        // 获取列表
        let opt = {
          // keyword: this.searchForm.keyword,
          pageNum: this.pageNum,
          // pageSize: 15
          status: 1
        }
        let data = await api.getStockXg(opt)
        if (data.status === 0) {
          this.dataList = data.data.list
          console.log('🚀🚀 ~ this.dataList: ', this.dataList)
        } else {
          this.$message.error(data.msg)
        }
      },
      // 弹出大宗交易模态框
      showModel(item) {
        this.buyPopupVisible = true
        this.detail = item
        this.form.buyNums = ''
      },
      // 购买
      async buyStock() {
        let {form, detail, isIndexStock} = this
        this.loading = true
        let opts = {
          newlistId: this.detail.newlistId,
          buyNums: form.buyNums,
        }
        let stockApi = api.buyStockXgqc
        // if (isIndexStock) {
        //   Object.assign(opts, {indexId: this.detail.id})
        //   stockApi = api.indexBuy
        // }
        try {
          let data = await stockApi(opts)
          console.log(data)
          if (data.status === 0) {
            this.$message({
              message: '申购成功',
              type: 'success'
            })
            this.buyPopupVisible = false
          } else {
            this.$message({
              message: '申购失败',
              type: 'warning'
            })
          }
        } catch (e) {
        }
      },
      async getlist1() {
        let opt = {
          pageNum: this.pageNum,
          // pageSize: this.pageSize
        }
        let data = await api.getStockXgqc(opt)
        this.dataList1 = data.data.list
      },
      async getlist2() {
        let opt = {
          status: 3,
          pageNum: this.pageNum,
          // pageSize: this.pageSize
        }
        let data = await api.getStockXgqc(opt)
        this.dataList2 = data.data.list
      },
    },
  }
</script>
<style lang="less" scoped>
  /deep/ .el-input__inner {
    background-color: #fff !important;
    border: 1px solid #DCDFE6 !important;
    color: #333 !important;
  }

  /deep/ .cell {
    color: #fff !important;
  }

  /deep/ .aaaa .el-tabs__item {
    color: #d8d8d8 !important;
  }

  /deep/ .el-tabs__item.is-active {
    color: #409eff !important;
  }

  /deep/ .black-bg .futuresholdposition .el-table th .cell {
    color: #fff !important;
  }

  .red {
    color: red !important;
  }

  .green {
    color: #17b780 !important;
  }

  .number {
    background-color: rgba(250, 250, 250, 0) !important;
  }

  .table {
    min-height: 500px;

    .code {
      color: #6d718b;
      font-size: 12px;
    }

    .more-btn {
      text-align: center;
      color: #8f92a3;
    }

    /deep/ th.el-table_1_column_1 {
      padding-left: 50px;
    }
  }

  .con-box {
    // color: #fff;
    padding: 0 20px;

    .box-account {
      padding: 20px 10px 0;

      .name {
        // font-size: 16px;
      }

      .account {
        // font-size: 46px;
        font-weight: 400;
      }

      .el-col {
        padding: 10px;
      }

      .box {
        padding: 10px;
        padding-left: 50px;
        border-bottom: 1px solid rgba(230, 230, 230, 0.6);

        .title {
          // font-size: 16px;
          color: #333;
          margin-bottom: 10px;
        }
      }

      .box1 {
        border-bottom: none;
      }

      .box-btn {
        padding: 12px;
      }

      .number {
        // font-size: 18px;
      }
    }
  }

  .user-info {
    padding: 20px 0;

    .el-row {
      margin-bottom: 15px;
      line-height: 30px;
      height: 30px;
    }

    .name {
      width: 96px;
      text-align: right;
      display: inline-block;
      color: #6e6e6e;
    }

    .info {
      font-size: 16px;
    }

    .btn-statue {
      margin-left: 100px;
      margin-bottom: 20px;
    }
  }

  .progress-box {
    position: relative;
    margin-bottom: 20px;

    .item {
      position: absolute;
      width: 46%;
      height: 30px;
      top: 38%;
      left: 27%;
      background: #fff;
    }

    .progress-title {
      font-size: 16px;
      padding: 0 12px;
      margin-top: 10px;
    }

    /deep/ .el-progress {
      .el-progress-bar__outer {
        background-color: #ff9800;
      }

      .el-progress-bar__inner {
        background-color: #ff5722;
      }

      .el-progress-bar__innerText {
        color: #ff5722;
        font-size: 0;
      }
    }
  }

  .el-tabs {
    margin-top: 30px;
  }

  .force-line {
    // margin-top: 30px;
    // background-color: #fdf6ec;
    color: #e6a23c;

    p {
      padding: 8px 16px;
    }

    .number {
      font-size: 18px;
    }
  }

  .box-btn {
    margin-top: 20px;

    .el-button {
      padding-left: 50px;
      padding-right: 50px;
    }
  }

  .Assets-box {
    // border: 1px solid #f1f1f1;
    border-radius: 8px;
    box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
    padding: 10px 0;
    margin: 0 !important;

    .box {
      position: relative;

      .iconfont {
        // position: absolute;
        // margin-right: 5px;
        font-size: 30px;
        // top: 17px;

        &.color1 {
          color: #2f97fc;
        }

        &.color2 {
          color: #17b780;
        }

        &.color3 {
          color: #ff7602;
        }

        &.color3 {
          color: #fd4256;
        }

        &.color4 {
          color: #fda822;
        }
      }
    }
  }

  .account-all {
    margin-top: 20px;

    .title {
      font-size: 16px;
      line-height: 30px;

      span {
        font-size: 12px;
        color: #777;
      }
    }

    .number {
      font-size: 22px;
      margin-top: 4px;
      text-shadow: 1px 2px 2px rgba(24, 24, 24, 0.3);
    }
  }

  .code {
    font-size: 12px;
    color: #fff;
  }

  .main-wrapper {
    .wrapper {
      padding: 20px;

      .table-search {
        margin-bottom: 15px;
      }
    }
  }
</style>
