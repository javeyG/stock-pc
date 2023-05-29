<template>
  <el-container class="user-center">
    <el-header class="user-header">
      <home-header></home-header>
    </el-header>
    <div class="usercot">
      <el-container class="main-wrapper">
        <el-aside width="178px">
          <menu-box></menu-box>
        </el-aside>
        <el-main>
          <div data-v-5d396ccb class="user-center-title" style="text-align: left;margin: 15px 10px 10px 10px;">
            <span data-v-5d396ccb class="iconfont icon-you" style="color: rgb(193, 24, 21); font-size: 18px; margin-right: 10px;"></span>
            修改银行卡
          </div>
          <div class="wrapper yhk-cont">
            <div class="box page-part transaction">
              <div class="box-contain clearfix">
                <div class="img-cont">
                  <div class="box-img"></div>
                </div>
                <p v-if="!bankInfo"  style="color: #e6003e;" class="tianjia">
                  <span class="iconfont icon-quan-cuo"></span>
                  未添加
                </p>
                <div class="auth-box">
                  <el-form :hide-required-asterisk='true' :model="form" label-width="100px" ref="ruleForm" :rules="rule" class="demo-form-inline">
                    <el-form-item label="银行名称" prop="bankName">
                      <el-input type='text' class="chongzhi-input" v-model="form.bankName" placeholder="例:中国工商银行（全称）">
                      </el-input>
                    </el-form-item>
                    <el-form-item label="开户支行" prop="bankAddress">
                      <el-input type='text' class="chongzhi-input" v-model="form.bankAddress" placeholder="需要精确到分行或者支行">
                      </el-input>
                    </el-form-item>
                    <el-form-item label="银行卡号" prop="bankNo">
                      <el-input type='text' class="chongzhi-input" v-model="form.bankNo" placeholder="请输入银行卡号">
                      </el-input>
                    </el-form-item>
                  </el-form>

                  <div class="rule-box">
                    <div class="title">提示：</div>
                    <ul>
                      <li>1、新用户注册后必须通过添加银行卡。</li>
                      <li>2、用户只能添加一张本人银行卡。</li>
                      <li>3、真实姓名必须和出金银行卡户名一致。</li>
                    </ul>
                  </div>
                  <div slot="footer" class="dialog-footer">
                    <el-button type="primary" :loading="isloading" @click="submit('ruleForm')">修改</el-button>
                  </div>
                </div>

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
  import HomeHeader from '../../../../components/HeaderOrder'
  import HomeFooter from '../../../../components/Footer'
  import MenuBox from '../menu'
  import * as api from '../../../../axios/api'
  import {getBankCard, updateBankCard} from '../../../../axios/api'

  export default {
    components: {
      HomeHeader,
      HomeFooter,
      MenuBox
    },
    props: {},
    data() {
      let validatePass = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请输入银行卡号'))
        } else {
          let myreg = /^([1-9]{1})(\d{14,18})$/ // 卡号校验
          if (!myreg.test(value)) {
            callback(new Error('请输入正确的银行卡号'))
          }
          callback()
        }
      }
      return {
        isAdded: true, // 是否添加银行卡了
        hasAuth: false,
        isloading: false,
        bankInfo: null,
        form: {
          bankName: '',
          bankAddress: '',
          bankNo: ''
        },
        rule: {
          bankNo: [
            {
              required: true,
              message: '请输入银行卡号',
              trigger: 'blur',
              // validator: validatePass,
            },
          ],
          bankName: [
            {
              required: true,
              message: '请输入银行名称',
              trigger: 'blur'
            },
          ],
          bankAddress: [
            {
              required: true,
              message: '请输入开户支行',
              trigger: 'blur'
            },
          ]

        }
      }
    },
    watch: {},
    computed: {},
    created() {
    },
    mounted() {
      api.getBankCard().then(res => {
        this.bankInfo = res.data
        if (this.bankInfo) {
          for (let i in this.form) {
            if (i in this.bankInfo) {
              this.form[i] = this.bankInfo[i]
            }
          }
        } else {
          this.isAdded = false
        }
      })
      this.$store.state.userMenu = '2-13'
    },
    methods: {
      submit(formName) {
        // 提交
        this.$refs[formName].validate(async (valid) => {
          if (valid) {
            // 修改密码
            let opts = {
              bankName: this.form.bankName,
              bankNo: this.form.bankNo,
              bankAddress: this.form.bankAddress,
            }
            let handle = this.isAdded ? api.updateBankCard : api.addBankCard
            let data = await handle(opts)
            if (data.status === 0) {
              this.$message.success(data.data)
            } else {
              this.$message.error(data.data)
            }
          } else {
            return false
          }
        })
      }
    }
  }
</script>
<style lang="less" scoped>
  /deep/ .el-input__inner {
    border-radius: 50px;
  }


  .wrapper .demo-form-inline {
    margin: 0 !important;
  }

  .wrapper {
    padding: 0 !important;
  }

  .transaction {
    width: 100%;
  }

  .wrapper .demo-form-inline {
    width: 90% !important;
  }

  .wrapper .auth-box .dialog-footer {
    width: 90% !important;
    margin: 0 !important;
  }

  .wrapper .auth-box .dialog-footer .el-button {
    width: 89% !important;
    border-radius: 50px;
  }

  .red-bg {

    .yhk-cont {


      display: flex;
      justify-content: center;
      align-items: center;

      .img-cont {
        display: flex;
        align-items: center;
        justify-content: center;

        .box-img {

          width: 100px;
          height: 100px;
          background: url("../../../../assets/image/suo2.png") no-repeat center center;
        }
      }


      .yhk-list {
        padding: 30px;
        width: 100%;
        margin: 0 60px;
        border-bottom: 1px solid #ccc;
      }
    }

    .yanzeng-cont {

      display: flex;

      .tongguo,
      .renzheng {
        border-radius: 3px;
        padding: 2px 5px;
        display: inline-block;
        background-color: rgb(4, 130, 62);
        margin: 0 5px;
        color: #fff;
      }

      .renzheng {
        background-color: rgb(230, 0, 62);
      }

      .yanzeng-left {
        height: 60px;
        width: 60px;

        img {
          width: 100%;
          height: 100%;
        }
      }

      .yanzeng-right {
        margin-left: 10px;
      }

      .yanzeng-right > div {
        margin-bottom: 20px;
        // margin-left: 10px;
      }

      .zfz {
        display: flex;

        .fan {
          margin-left: 30px;
        }
      }
    }

    .youyi {
      color: #000;
    }

    .chongzhi-bizhi {
      .right {
        width: 106px;
        height: 134px;

        background: url("../../../../assets/image/youqingtishi1.png") no-repeat;
      }

      margin-top: 50px;

      background-color: #e5e5e5;
      display: flex;
      padding: 40px;
      justify-content: space-between;

      .chongzhi-bizhi-cont .chongzhi-item {
        margin: 15px 0;
        color: #000;

        .circle {
          display: inline-block;
          width: 15px;
          text-align: center;
          height: 15px;
          border: 1px solid rgb(121, 121, 121);
          border-radius: 50%;
          line-height: 15px;
        }
      }
    }

    .chongzhi-cont {
      position: relative;

      .left {
        position: absolute;
        left: 50%;
        transform: translateX(-50%);
      }

      .xianshi-cont {
        display: flex;
        justify-content: space-between;
      }

      .chongzhi-img {
        display: flex;
        align-items: center;
        justify-content: center;
      }

      .chongzhi-type {
        display: flex;
        margin-top: 20px;
        margin-bottom: 20px;

        .chongzhi-type-item {
          margin-right: 20px;
        }
      }

      .chongzhi-type > span {
        display: inline-block;
        width: 85px;
      }

      .chongzhi-btn-cont {
        display: flex;
        justify-content: center;
        margin-top: 10px;

        .chongzhi-btn {
          width: 80%;
          border-radius: 50px;
          text-align: center;
          height: 40px;
          background-color: #c11815;
          color: #fff;
          line-height: 40px;
          font-size: 14px;
          font-family: Microsoft YaHei;
          font-weight: 400;
          margin-top: 20px;
        }
      }

      .chongzhi-input {
        display: flex;
        align-items: center;
        width: 90%;
        background: none;
      }

      .chongzhi-input > span {
        display: inline-block;
        width: 100px;
      }
    }
  }

  .black-bg {
    .yhk-cont {
      display: flex;
      justify-content: center;
      align-items: center;

      .img-cont {
        display: flex;
        align-items: center;
        justify-content: center;

        .box-img {

          width: 100px;
          height: 100px;
          background: url("../../../../assets/image/suo.png") no-repeat center center;
        }
      }


      .yhk-list {
        padding: 30px;
        width: 100%;
        margin: 0 60px;
        border-bottom: 1px solid #383838;
      }
    }

    .el-button--primary {
      border: none;
      background-color: rgb(2, 77, 161) !important;
    }

    .yanzeng-cont {
      display: flex;

      .tongguo,
      .renzheng {
        padding: 2px 5px;
        display: inline-block;
        background-color: rgb(4, 130, 62);
        border-radius: 2px;
        margin: 0 5px;
      }

      .renzheng {
        background-color: rgb(230, 0, 62);
      }

      .yanzeng-left {
        height: 60px;
        width: 60px;

        img {
          width: 100%;
          height: 100%;
        }
      }

      .yanzeng-right {
        margin-left: 10px;
      }

      .yanzeng-right > div {
        margin-bottom: 20px;
        // margin-left: 10px;
      }

      .zfz {
        display: flex;

        .fan {
          margin-left: 30px;
        }
      }
    }

    .youyi {
      color: #fff;
    }

    .chongzhi-bizhi {
      .right {
        width: 106px;
        height: 134px;

        background: url("../../../../assets/image/youqingtishi.png") no-repeat;
      }

      margin-top: 50px;

      background-color: #191e2b;
      display: flex;
      padding: 40px;
      justify-content: space-between;

      .chongzhi-bizhi-cont .chongzhi-item {
        margin: 15px 0;
        color: #7e8086;

        .circle {
          display: inline-block;
          width: 15px;
          text-align: center;
          height: 15px;
          border: 1px solid rgb(121, 121, 121);
          border-radius: 50%;
          line-height: 15px;
        }
      }
    }

    .chongzhi-cont {
      position: relative;

      .left {
        position: absolute;
        left: 50%;
        transform: translateX(-50%);
      }

      .xianshi-cont {
        display: flex;
        justify-content: space-between;
      }

      .chongzhi-img {
        display: flex;
        align-items: center;
        justify-content: center;
      }

      .chongzhi-type {
        display: flex;
        margin-top: 20px;
        margin-bottom: 20px;

        .chongzhi-type-item {
          margin-right: 20px;
        }
      }

      .chongzhi-type > span {
        display: inline-block;
        width: 85px;
      }

      .chongzhi-btn-cont {
        display: flex;
        justify-content: center;
        margin-top: 10px;

        .chongzhi-btn {
          width: 80%;
          border-radius: 50px;
          text-align: center;
          height: 40px;
          background-color: #c11815;
          color: #fff;
          line-height: 40px;
          font-size: 14px;
          font-family: Microsoft YaHei;
          font-weight: 400;
          margin-top: 20px;
        }
      }

      .chongzhi-input {
        display: flex;
        align-items: center;
        width: 90%;
        background: none;
      }

      .chongzhi-input > span {
        display: inline-block;
        width: 100px;
      }
    }
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
    padding: 40px 100px 80px 0;

    .demo-form-inline {
      width: 400px;
      margin: 20px auto;
    }

    .auth-box {
      .dialog-footer {
        width: 400px;
        margin: 0 auto;

        .el-button {
          margin-left: 100px;
          width: 300px;
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
      margin: 30px 0 30px 20px;
      line-height: 26px;
      color: #666;
    }
  }
</style>
