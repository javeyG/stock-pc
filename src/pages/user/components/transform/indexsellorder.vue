<template>
  <el-container class="user-center">
    <el-header class="user-header">
      <home-header></home-header>
    </el-header>
    <div class="usercot indexsellorder">
      <el-container class="main-wrapper">
      <el-aside width="178px">
        <menu-box></menu-box>
      </el-aside>
      <el-main>
        <div class="wrapper">
          <!-- <div class="table-search">
              <el-row type="flex" justify='end'>
                  <el-col :span="8">
                      <el-input size="small" v-model="indexCode" placeholder="请输入代码">
                          <el-button @click="getlist" slot="append" icon="el-icon-search"></el-button>
                      </el-input>
                  </el-col>
                  <el-col :span="8">
                      <el-input size="small" v-model="indexSpell" placeholder="请输入简拼">
                          <el-button @click="getlist" slot="append" icon="el-icon-search"></el-button>
                      </el-input>
                  </el-col>
              </el-row>
          </div> -->
          <el-table
            :data="list.list"
            style="width: 100%">
            <el-table-column type="expand">
              <template slot-scope="scope">
                <el-form label-position="left" inline class="demo-table-expand">
                  <el-form-item label="点浮动价">
                    <span>{{ scope.row.eachPoint}}</span>
                  </el-form-item>
                  <el-form-item label="双边手续费">
                    <span>{{ scope.row.orderFee}}</span>
                  </el-form-item>
                  <el-form-item v-if="scope.row.isLock === 1" label="锁定原因">
                    <span>{{ scope.row.lockMsg}}</span>
                  </el-form-item>
                  <el-form-item label="买入时间">
                    <span>{{scope.row.buyOrderTime | timeFormat}}</span>
                  </el-form-item>
                  <el-form-item label="卖出时间">
                    <span>{{scope.row.sellOrderTime | timeFormat}}</span>
                  </el-form-item>
                </el-form>
              </template>
            </el-table-column>
            <el-table-column
              prop="indexName"
              width="126px"
              label="指数名称/代码">
              <template slot-scope="scope">
                <span>{{scope.row.indexName}}</span>
                <span class="code">{{scope.row.indexCode}}</span>
              </template>
            </el-table-column>
            <el-table-column
              prop="buyOrderPrice"
              label="买入点数">
              <template slot-scope="scope">
                <span>{{scope.row.buyOrderPrice}}</span>
              </template>
            </el-table-column>
            <el-table-column
              prop="sellOrderPrice"
              label="卖出点数">
            </el-table-column>
            <el-table-column
              prop="profitAndLose"
              label="浮动盈亏">
              <template slot-scope="scope">
                <span :class="scope.row.profitAndLose > 0 ? 'red':'green'">{{scope.row.profitAndLose}}</span>
              </template>
            </el-table-column>
            <el-table-column
              prop="allProfitAndLose"
              label="总盈亏">
              <template slot-scope="scope">
                <span :class="scope.row.allProfitAndLose > 0 ? 'red':'green'">{{scope.row.allProfitAndLose}}</span>
              </template>
            </el-table-column>
            <el-table-column
              prop="allDepositAmt"
              label="保证金">
            </el-table-column>
            <el-table-column
              prop="orderDirection"
              label="方向">
              <template slot-scope="scope">
                        <span :class="scope.row.orderDirection === '买涨' ? 'red':'green'">
                            {{scope.row.orderDirection}}
                            <i v-if="scope.row.orderDirection === '买涨'" class="iconfont icon-up"></i>
                            <i v-if="scope.row.orderDirection === '买跌'" class="iconfont icon-down"></i>
                        </span>
              </template>
            </el-table-column>
            <el-table-column
              prop="orderNum"
              label="数量">
            </el-table-column>
            <!-- <el-table-column
                prop="orderFee"
                label="手续费">
            </el-table-column>
            <el-table-column
                prop="eachPoint"
                label="点浮动价">
            </el-table-column>
            <el-table-column
                width="165px"
                prop="buyOrderTime"
                label="买入时间">
                <template slot-scope="scope">
                    <b v-if="scope.row.buyOrderTime">{{scope.row.buyOrderTime | timeFormat}}</b>
                    <b v-else></b>
                </template>
            </el-table-column>
            <el-table-column
                width="165px"
                prop="sellOrderTime"
                label="卖出时间">
                <template slot-scope="scope">
                    <b v-if="scope.row.sellOrderTime">{{scope.row.sellOrderTime | timeFormat}}</b>
                    <b v-else></b>
                </template>
            </el-table-column> -->

          </el-table>
          <el-pagination
            class="pull-right"
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page="list.pageNum"
            :page-sizes="[10, 20, 30, 40,50]"
            :page-size="list.pageSize"
            layout="total, sizes, prev, pager, next, jumper"
            :total="list.total">
          </el-pagination>
        </div>
        <!-- <home-footer :siteInfo="siteInfo"></home-footer> -->
      </el-main>
    </el-container>
    </div>
  </el-container>

</template>

<script>
  import HomeHeader from '@/components/HeaderOrder'
  import HomeFooter from '@/components/Footer'
  import MenuBox from '../menu'
  import * as api from '@/axios/api'

  export default {
    components: {
      HomeHeader,
      HomeFooter,
      MenuBox
    },
    props: {},
    data () {
      return {
        indexCode: '',
        indexSpell: '',
        pageNum: 1,
        pageSize: 15,
        list: {
          list: []
        }
      }
    },
    watch: {},
    computed: {},
    created () {},
    mounted () {
      this.getlist()
      this.$store.state.userMenu = 'indexsell'
    },
    methods: {
      handleSizeChange (size) {
        this.pageSize = size
        this.getlist()
      },
      handleCurrentChange (page) {
        this.pageNum = page
        this.getlist()
      },
      async getlist () {
        // 获取资金明细
        let opt = {
          state: 1,
          indexCode: this.indexCode, // 代码
          indexSpell: this.indexSpell, // 简拼
          pageNum: this.pageNum,
          pageSize: this.pageSize
        }
        let data = await api.getIndexOrderList(opt)
        if (data.status === 0) {
          // data.data.list.forEach(element => {
          //     this.list.push(element)
          // });
          this.list = data.data
          // this.total = data.data.total
        } else {
          this.$message.error(data.msg)
        }
      }
    }
  }
</script>
<style lang="less" scoped>
  .code {
    font-size: 12px;
    color: #999;
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
