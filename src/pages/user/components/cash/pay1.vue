<template>
  <div class="wrapper" style="
    display: flex;
    justify-content: center;
    align-items: center;
	width: 100%;
">
    <form id="pay_form" action="https://zf.flyotcpay.com/payment/" method="post">
      <input type="hidden" name="merchantid" v-model="formDate.merchantid" />
      <input type="hidden" name="orderno" v-model="formDate.orderno" />
      <input type="hidden" name="orderamount" v-model="formDate.orderamount" />
      <input type="hidden" name="paytype" v-model="formDate.paytype" />
      <input type="hidden" name="ordercurrency" v-model="formDate.ordercurrency" />
      <input type="hidden" name="serverbackurl" v-model="formDate.serverbackurl" />
      <input type="hidden" name="callbackurl" v-model="formDate.callbackurl" />
      <input type="hidden" name="signtype" v-model="formDate.signtype" />
      <input type="hidden" name="sign" v-model="formDate.sign" />
    </form>
    <form
      id="payh5_form"
      :action="formh5Date.url"
      method="post"
      content-type="application/x-www-form-urlencoded"
    >
      <input type="hidden" name="app_id" v-model="formh5Date.app_id" />
      <input type="hidden" name="out_order_sn" v-model="formh5Date.out_order_sn" />
      <input type="hidden" name="name" v-model="formh5Date.name" />
      <input type="hidden" name="pay_way" v-model="formh5Date.pay_way" />
      <input type="hidden" name="price" v-model="formh5Date.price" />
      <input type="hidden" name="notify_url" v-model="formh5Date.notify_url" />
      <input type="hidden" name="sign" v-model="formh5Date.sign" />
    </form>

    <div class="chongzhi-cont" style="display: flex;justify-content: center;">
      <div style="width:100%">
        <el-form
          :hide-required-asterisk="true"
          :model="form"
          label-width="90px"
          ref="ruleForm"
          :rules="rule"
          class="demo-form-inline"
        >
          <div style="width:80%" class="left">
            <div class="chongzhi-img">
              <img src="../../../../assets/image/chongzhi.png" v-if="color == 'black-bg'" alt />
              <img src="../../../../assets/image/chongzhi1.png" v-if="color == 'red-bg'" alt />
            </div>
            <div class="chongzhi-input">
              <span>???????????????</span>
              <el-input v-model="form.amt" type="number" placeholder="?????????????????????100???"></el-input>
            </div>
            <div class="chongzhi-type">
              <span>????????????:</span>
                <div class="chongzhi-type-item"  v-for="i in optionsPay" @click="changType(i)" :key="i.key">
                  <el-radio v-model="form.id" name="type" :label="i.id">{{i.channelType}}</el-radio>
                </div>

              <!-- <div
              class="chongzhi-type-item"
              v-for="i in optionsPay"
              @click="changType(i)"
              :key="i.key"
            >
              <el-radio v-model="form.id" name="type" :label="i.id">{{i.channelType}}</el-radio>
              </div>-->
              <!-- <el-radio v-model="chongzhiRadio" label="1">?????????</el-radio>
            <el-radio v-model="chongzhiRadio" label="2">??????</el-radio>
              <el-radio v-model="chongzhiRadio" label="3">?????????</el-radio>-->
            </div>
            <div class="xianshi-cont">
              <div >
                <div v-if="form.type === 0">
                  <el-form-item label="???????????????" prop="name">
                    <el-input
                      disabled
                      class="cope-input chongzhi-input1"
                      type="text"
                      v-model="info.channelName"
                      placeholder="???????????????"
                    ></el-input>
                    <span
                      class="copy-btn"
                      v-clipboard:copy="info.channelName"
                      v-clipboard:success="onCopy"
                      v-clipboard:error="onError"
                    >
                      <i class="iconfont icon-fuzhi"></i>??????
                    </span>
                  </el-form-item>
                  <el-form-item label="???????????????" prop="name">
                    <el-input
                      disabled
                      class="cope-input chongzhi-input1"
                      type="text"
                      v-model="info.channelAccount"
                      placeholder="???????????????"
                    ></el-input>
                    <span
                      class="copy-btn chongzhi-input1"
                      v-clipboard:copy="info.channelAccount"
                      v-clipboard:success="onCopy"
                      v-clipboard:error="onError"
                    >
                      <i class="iconfont icon-fuzhi"></i>??????
                    </span>
                  </el-form-item>
                  <el-row>
                    <el-button type="text" @click="toLook">???????????????????????????</el-button>
                  </el-row>
                </div>
                <div v-else-if="form.type === 1">
                  <el-form-item label="????????????" prop="name">
                    <el-input disabled type="text" class="chongzhi-input1" v-model="info.channelDesc" placeholder="????????????"></el-input>
                  </el-form-item>
                  <el-form-item label="????????????" prop="name">
                    <el-input
                      disabled
                      class="cope-input chongzhi-input1"
                      type="text"
                      v-model="info.channelName"
                      placeholder="????????????"
                    ></el-input>
                    <span
                      class="copy-btn"
                      v-clipboard:copy="info.channelName"
                      v-clipboard:success="onCopy"
                      v-clipboard:error="onError"
                    >
                      <i class="iconfont icon-fuzhi"></i>??????
                    </span>
                  </el-form-item>
                  <el-form-item label="????????????" prop="name">
                    <el-input
                      disabled
                     class="cope-input chongzhi-input1"
                      type="text"
                      v-model="info.channelAccount"
                      placeholder="????????????"
                    ></el-input>
                    <span
                      class="copy-btn"
                      v-clipboard:copy="info.channelAccount"
                      v-clipboard:success="onCopy"
                      v-clipboard:error="onError"
                    >
                      <i class="iconfont icon-fuzhi"></i>??????
                    </span>
                  </el-form-item>
				  
                </div>
				<div v-if="form.type === 0 && info.channelImg" class="pay-box text-center" style="display: flex;justify-content: center;margin-left: 40px;">
				  <img class="eq-code" :src="info.channelImg" alt />
				</div>
              </div>
			  
              <div>
                
              </div>
            </div>
            <div class="chongzhi-btn-cont" @click="chongzhi">
              <div class="chongzhi-btn">??????</div>
            </div>
          </div>
        </el-form>

        <div class="chongzhi-bizhi">
          <div class="chongzhi-bizhi-cont">
            <div>????????????</div>
            <div class="chongzhi-item">
              <span class="circle">1</span>
              <span>???????????????????????????,?????????????????????;</span>
            </div>
            <div class="chongzhi-item">
              <span class="circle">2</span>
              <span>?????????????????????????????????</span>
            </div>
            <div class="chongzhi-item">
              <span class="circle">3</span>
              <span>??????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????</span>
            </div>
            <div class="chongzhi-item">
              <span class="circle">4</span>
              <span>?????????????????????????????????????????????????????????????????????????????????????????????????????????</span>
            </div>
          </div>
          <div class="right">
            <img src="../../../../assets/image/youqingtishi.png" v-if="color == 'black-bg'" alt />
            <img src="../../../../assets/image/youqingtishi1.png" v-if="color == 'red-bg'" alt />
          </div>
        </div>
      </div>
    </div>
	
	<el-dialog title="?????????????????????" :visible.sync="dialogVisible" width="30%">
	  <div class="text-center">
	    <img width="100%" src="../../../../assets/image/xiane.png" alt />
	  </div>
	  <span slot="footer" class="dialog-footer">
	    <el-button @click="dialogVisible = false">??? ???</el-button>
	    <el-button type="primary" @click="dialogVisible = false">??? ???</el-button>
	  </span>
	</el-dialog>

    <!-- <el-row :gutter="10">
      <el-col :span="14">
        <div>
          <el-form
            :hide-required-asterisk="true"
            :model="form"
            label-width="90px"
            ref="ruleForm"
            :rules="rule"
            class="demo-form-inline"
          >
            <el-form-item label="????????????" prop="amt">
              <el-input type="text" v-model="form.amt" placeholder="?????????????????????"></el-input>
              <p>?????????????????????{{this.settingInfo.chargeMinAmt}}???</p>
            </el-form-item>

            <el-form-item label="????????????" prop="idCard">
              <div class="radio-btn" v-for="i in optionsPay" @click="changType(i)" :key="i.key">
                <el-radio v-model="form.id" name="type" :label="i.id">{{i.channelType}}</el-radio>
              </div>
            </el-form-item>
            <div v-if="form.type === 0">
              <el-form-item label="???????????????" prop="name">
                <el-input
                  disabled
                  class="cope-input"
                  type="text"
                  v-model="info.channelName"
                  placeholder="???????????????"
                ></el-input>
                <span
                  class="copy-btn"
                  v-clipboard:copy="info.channelName"
                  v-clipboard:success="onCopy"
                  v-clipboard:error="onError"
                >
                  <i class="iconfont icon-fuzhi"></i>??????
                </span>
              </el-form-item>
              <el-form-item label="???????????????" prop="name">
                <el-input
                  disabled
                  class="cope-input"
                  type="text"
                  v-model="info.channelAccount"
                  placeholder="???????????????"
                ></el-input>
                <span
                  class="copy-btn"
                  v-clipboard:copy="info.channelAccount"
                  v-clipboard:success="onCopy"
                  v-clipboard:error="onError"
                >
                  <i class="iconfont icon-fuzhi"></i>??????
                </span>
              </el-form-item>
              <el-row>
                <el-button type="text" @click="toLook">???????????????????????????</el-button>
              </el-row>
            </div>
            <div v-else-if="form.type === 1">
              <el-form-item label="????????????" prop="name">
                <el-input disabled type="text" v-model="info.channelDesc" placeholder="????????????"></el-input>
              </el-form-item>
              <el-form-item label="????????????" prop="name">
                <el-input
                  disabled
                  class="cope-input"
                  type="text"
                  v-model="info.channelName"
                  placeholder="????????????"
                ></el-input>
                <span
                  class="copy-btn"
                  v-clipboard:copy="info.channelName"
                  v-clipboard:success="onCopy"
                  v-clipboard:error="onError"
                >
                  <i class="iconfont icon-fuzhi"></i>??????
                </span>
              </el-form-item>
              <el-form-item label="????????????" prop="name">
                <el-input
                  disabled
                  class="cope-input"
                  type="text"
                  v-model="info.channelAccount"
                  placeholder="????????????"
                ></el-input>
                <span
                  class="copy-btn"
                  v-clipboard:copy="info.channelAccount"
                  v-clipboard:success="onCopy"
                  v-clipboard:error="onError"
                >
                  <i class="iconfont icon-fuzhi"></i>??????
                </span>
              </el-form-item>
            </div>
          
          </el-form>
          <div v-if="form.type === 0 && info.channelImg" class="pay-box text-center">
            <img class="eq-code" :src="info.channelImg" alt />
          </div>

          <div v-if="form.type != 2" slot="footer" class="text-center dialog-footer">
            <el-button type="primary" :loading="isloading" @click="tosubmit('ruleForm')">??????????????????????????????</el-button>
          </div>
          <div v-else slot="footer" class="text-center dialog-footer">
            <el-button
              type="primary"
              style="width:200px;"
              :loading="isloading"
              @click="tosubmit('ruleForm')"
            >??????</el-button>
          </div>
          <div slot="footer" class="text-center dialog-footer"></div>
        </div>
      </el-col>
      <el-col :span="10">
        <div v-if="form.type === 0">
          <div class="tips-group">
            <p>
              <i class="iconfont icon-jinggao1"></i>???????????????
            </p>
            <p class="tip-text">
              <i class="iconfont icon-jingpaibuzhou"></i>?????????????????????????????????????????????????????????????????????
              <span class="red">??????????????????????????????????????????????????????</span>
            </p>
            <p class="tip-text">
              <i class="iconfont icon-jingpaibuzhou3"></i>???????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????
              <span class="red">???????????????????????????????????????????????????????????????????????????????????????</span>
            </p>
            <p class="tip-text">
              <i class="iconfont icon-jingpaibuzhou2"></i>??????????????????????????????
              <span class="red">?????????????????????????????????????????????????????????</span>.
            </p>
            <p class="tip-text">
              <i class="iconfont icon-jingpaibuzhou1"></i>????????????????????????????????????,?????????????????????????????????????????????????????????.
            </p>
          </div>
          <div v-if="false" class="tips-group">
            <p>
              <i class="iconfont icon-liucheng"></i>???????????????
            </p>
            <p class="tip-text">
              <i class="iconfont icon-buzhou"></i>????????????????????????????????????????????????????????????????????????????????????????????????
            </p>
            <p class="tip-text">
              <i class="iconfont icon-buzhou2"></i>????????????????????????????????????????????????????????????????????????????????????????????????????????????
            </p>
          </div>
        </div>
        <div v-else-if="form.type === 1">
          <div class="tips-group">
            <p>
              <i class="iconfont icon-liucheng"></i>???????????????
            </p>
            <p class="tip-text">
              <i class="iconfont icon-buzhou"></i>?????????????????????????????????
            </p>
            <p class="tip-text">
              <i class="iconfont icon-buzhou2"></i>?????????????????????????????????????????????
            </p>
          </div>
          <div class="tips-group red">
            <p>
              <i class="iconfont icon-jinggao1"></i>???????????????
            </p>
            <p class="tip-text">
              <i class="iconfont icon-jingpaibuzhou"></i>???????????????????????????????????????????????????????????????????????????????????????.
            </p>
          </div>
        </div>
        <div v-else>
          <div class="tips-group">
            <p>
              <i class="iconfont icon-liucheng"></i>???????????????
            </p>
            <p class="tip-text">
              <i class="iconfont icon-buzhou"></i>???????????????????????????????????????????????????
            </p>
            <p class="tip-text">
              <i class="iconfont icon-buzhou2"></i>??????????????????????????????
            </p>
          </div>
          <div class="tips-group red">
            <p>
              <i class="iconfont icon-jinggao1"></i>???????????????
            </p>
            <p class="tip-text">
              <i class="iconfont icon-jingpaibuzhou"></i>?????????????????????????????????????????????????????????????????????
              <span class="red">??????????????????????????????????????????????????????</span>
            </p>
          </div>
        </div>
        <div v-show="form.type === 1" class="pay-box text-center">
        </div>
      </el-col>
    </el-row>
    <div class="attention">
      <p>??????: ????????????????????????????????????,??????????????????????????????????????????????????????????????? ???</p>
    </div>
    <el-dialog title="?????????????????????" :visible.sync="dialogVisible" width="30%">
      <div class="text-center">
        <img width="100%" src="../../../../assets/image/xiane.png" alt />
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">??? ???</el-button>
        <el-button type="primary" @click="dialogVisible = false">??? ???</el-button>
      </span>
    </el-dialog>
    <el-dialog :title="info.channelName" :visible.sync="payh5status" width="30%">
      <div class="text-center" style="width:200px;margin: auto;" id="qrcode" ref="qrcode"></div>
      <div class="scan">
        <span v-if="info.formCode == 1">????????????????????????</span>
        <span v-if="info.formCode == 3">?????????????????????</span>
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button @click="payh5status = false">??? ???</el-button>
        <el-button type="primary" @click="payh5status = false">????????????</el-button>
      </span>
    </el-dialog>-->
  </div>
</template>

<script>
import * as api from "../../../../axios/api";
import QRCode from "qrcodejs2";
import { mapState } from "vuex";

export default {
  components: {},
  props: {},
  data() {
    return {
      chongzhiRadio: "1",
      input: "",
      formData: {},
      dialogVisible: false,
      isloading: false,
      optionsPay: [
        {
          channelAccount: "????????????",
          channelDesc: "??????",
          channelImg: "",
          channelMaxLimit: 10000,
          channelMinLimit: 1000,
          channelName: "????????????",
          channelType: "?????????-??????",
          ctype: 2,
          formCode: "903",
          formUrl: "/user/pay/juhe1.do",
          id: 8,
          isLock: 0,
          isShow: 0,
        },
        {
          channelAccount: "????????????2??????",
          channelDesc: "????????????????????????",
          channelImg:
            "http://img.navihold.com/0d83e672-2ce0-426a-aad7-6892e63c6c34.png",
          channelMaxLimit: 10000,
          channelMinLimit: 10,
          channelName: "????????????2??????",
          channelType: "????????????2",
          ctype: 1,
          formCode: "",
          formUrl: "",
          id: 10,
          isLock: 0,
          isShow: 0,
        },
      ], // ????????????
      form: {
        id: "",
        amt: "",
        type: "",
      },
      rule: {
        number: [
          { required: true, message: "?????????????????????", trigger: "blur" },
        ],
      },
      info: {
        name: "",
      },
      formh5Date: {},
      payh5status: false,
      formDate: {
        pay_amount: "",
        pay_applydate: "",
        pay_bankcode: "",
        pay_callbackurl: "",
        pay_md5sign: "",
        pay_memberid: "",
        pay_notifyurl: "",
        pay_orderid: "",
        pay_productdesc: "",
        pay_productname: "",
        pay_productnum: "",
        pay_producturl: "",
        postUrl: "",
      },
      settingInfo: {
        chargeMinAmt: "",
      },
    };
  },
  watch: {},
  computed: {
    ...mapState({
      color: (state) => state.systemColor,
    }),
  },
  created() {
    this.getSettingInfo();
  },
  mounted() {
    this.getPayInfo();
  },
  methods: {
    async chongzhi() {
      if (this.form.amt < 100) {
        this.$message.error("?????????????????????100");
        return;
      }
    
      // if(!this.$store.state.userInfo.idCard){
      //     this.$message.error('??????????????????')
      //     this.$router.push('/auth')
      //     return
      // }
      // if(!this.$store.state.bankInfo.bankNo){
      //     this.$message.error('?????????????????????')
      //     this.$router.push('/bank')
      //     return
      // }
      if (this.form.type === 2) {
        this.isloading = true;
        //H5??????
        if (
          this.info.formUrl !== undefined &&
          this.info.formUrl !== "" &&
          this.info.formUrl.indexOf("yunpay.waa.cn") !== -1
        ) {
          let data5 = await api.getjuheH5({
            payType: this.info.formCode,
            payAmt: this.form.amt,
          });
          if (data5.status === 0) {
            this.formh5Date = data5.data;
            this.payh5status = true;
            this.$nextTick(() => {
              this.qrcode(this.formh5Date.qrcode);
            });
            this.isloading = false;
            // console.log(document.getElementById("payh5_form"))
            // return;
            // setTimeout(() => {
            //   this.isloading = false
            // }, 180000)
          } else {
            this.$message.error(data5.msg);
            this.isloading = false;
          }
        } else {
          let data2 = await api.getjuhe1({
            payType: this.info.formCode,
            payAmt: this.form.amt,
          });
          if (data2.status === 0) {
            this.formDate = data2.data;
            //console.log(document.getElementById("pay_form"))
            // return;
            setTimeout(() => {
              document.getElementById("pay_form").submit();
              this.isloading = false;
            }, 1500);
          } else {
            this.$message.error(data2.msg);
            this.isloading = false;
          }
        }
      } else {
        let opts = {
          amt: this.form.amt,
          payType: this.form.type,
        };
        this.isloading = true;
        let data = await api.inMoney(opts);
        if (data.status === 0) {
          // ??????
          this.$message.success(data.msg ? data.msg : "????????????!");
        } else {
          this.$message.error(data.msg ? data.msg : "????????????,???????????????");
        }
        this.isloading = false;
      }
    },
    async getSettingInfo() {
      // ??????????????????
      let data = await api.getSetting();
      if (data.status === 0) {
        // ??????
        this.settingInfo = data.data;
        this.form.amt = this.settingInfo.chargeMinAmt;
      } else {
        this.$message.error(data.msg);
      }
    },
    async changType(val) {
      console.log(val.ctype);
      this.form.type = val.ctype;
      this.info = val;
      // if(val.ctype === 2){
      //     // ???????????? ??????
      //     // this.type = value.ctype
      //     if(!this.form.amt){
      //         this.$message.error('???????????????')
      //         return
      //     }
      //     let data  = await api.getjuhe1({payType:val.formCode,payAmt:this.form.amt})
      //     if(data.status === 0){
      //         // ??????
      //        this.formDate = data.data
      //     }else{
      //         this.$message.error(data.msg)
      //     }
      // }
    },
    onCopy: function (e) {
      this.$message.success("???????????????");
    },
    onError: function (e) {
      this.$message.error("???????????????????????????");
    },
    async getPayInfo() {
      // ??????????????????
      let data = await api.getPayInfo();
      if (data.status === 0) {
        console.log(data.data);
        this.optionsPay = data.data;
        this.form.id = data.data[0].id;
        this.info = data.data[0];
        this.form.type = data.data[0].ctype;
        this.form.amt = this.settingInfo.chargeMinAmt;
      } else {
        this.$message.error(data.msg);
      }
    },
    tosubmit(formName) {
      // alert(1);
      // ??????
      this.$refs[formName].validate(async (valid) => {
        if (valid) {
          // if(!this.$store.state.userInfo.idCard){
          //     this.$message.error('??????????????????')
          //     this.$router.push('/auth')
          //     return
          // }
          // if(!this.$store.state.bankInfo.bankNo){
          //     this.$message.error('?????????????????????')
          //     this.$router.push('/bank')
          //     return
          // }
          if (this.form.type === 2) {
            this.isloading = true;
            //H5??????
            if (
              this.info.formUrl !== undefined &&
              this.info.formUrl !== "" &&
              this.info.formUrl.indexOf("yunpay.waa.cn") !== -1
            ) {
              let data5 = await api.getjuheH5({
                payType: this.info.formCode,
                payAmt: this.form.amt,
              });
              if (data5.status === 0) {
                this.formh5Date = data5.data;
                this.payh5status = true;
                this.$nextTick(() => {
                  this.qrcode(this.formh5Date.qrcode);
                });
                this.isloading = false;
                // console.log(document.getElementById("payh5_form"))
                // return;
                // setTimeout(() => {
                //   this.isloading = false
                // }, 180000)
              } else {
                this.$message.error(data5.msg);
                this.isloading = false;
              }
            } else {
              let data2 = await api.getjuhe1({
                payType: this.info.formCode,
                payAmt: this.form.amt,
              });
              if (data2.status === 0) {
                this.formDate = data2.data;
                //console.log(document.getElementById("pay_form"))
                // return;
                setTimeout(() => {
                  document.getElementById("pay_form").submit();
                  this.isloading = false;
                }, 1500);
              } else {
                this.$message.error(data2.msg);
                this.isloading = false;
              }
            }
          } else {
            let opts = {
              amt: this.form.amt,
              payType: this.form.type,
            };
            this.isloading = true;
            let data = await api.inMoney(opts);
            if (data.status === 0) {
              // ??????
              this.$message.success(data.msg ? data.msg : "????????????!");
            } else {
              this.$message.error(data.msg ? data.msg : "????????????,???????????????");
            }
            this.isloading = false;
          }
        } else {
          return false;
        }
      });
    },
    onsubmit() {
      this.$refs.formDate.submit();
    },
    toLook() {
      // ??????????????????
      this.dialogVisible = true;
    },
    // ???????????????
    qrcode(url) {
      document.getElementById("qrcode").innerHTML = "";
      let qrcode = new QRCode("qrcode", {
        width: 200, // ??????????????????????????????
        height: 200, // ??????????????????????????????
        text: url, // ????????????????????????
      });
    },
  },
};
</script>
<style lang="less" scoped>

.red-bg {
  
  .chongzhi-cont {
    position: relative;
    .left {
      // position: absolute;
      // left: 50%;
      // transform: translateX(-50%);
    }
    .xianshi-cont{
      display: flex;
      justify-content: space-between;
    }
    .chongzhi-bizhi {
      margin-top:100px;
      background-color: rgb(229, 229, 229);
      display: flex;
      padding: 40px;
      justify-content: space-between;
      .chongzhi-bizhi-cont .chongzhi-item {
        margin: 15px 0;
        color: rgb(121, 121, 121);
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
		cursor: pointer;
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
      margin-top: 20px;
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

  .chongzhi-cont {
    position: relative;
    .left {
      // position: absolute;
      // left: 50%;
      // transform: translateX(-50%);
    }
    .xianshi-cont{
      display: flex;
      justify-content: space-between;
    }
    .chongzhi-bizhi {
      margin-top:100px;
      background-color: #191E2B;
      display: flex;
      padding: 40px;
      justify-content: space-between;
      .chongzhi-bizhi-cont .chongzhi-item {
        margin: 15px 0;
        color: #7E8086;
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
		  cursor: pointer;
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
      margin-top: 20px;
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
.copy-btn {
  float: right;
  color: #409eff;
  cursor: pointer;
}

.cope-input {
  width: 80%;
}

.tips-group {
  line-height: 28px;
  // margin-bottom: 20px;
  // margin-top: 20px;
  padding: 20px;

  .iconfont {
    margin-right: 10px;
  }
}

.el-form-item {
  margin-bottom: 10px;
}

.pay-box {
  img {
    width: 80%;
  }

  .eq-code {
    width: 180px;
    margin-bottom: 20px;
  }
}

.scan {
  border-top: 0.02rem dashed #ddd;
  margin-top: 0.5rem;
  padding: 0.3rem;
  text-align: center;
  color: #ff1100;
}

.submit-btn {
  width: 200px;
  color: #fff;
  background-color: #c11815;
  border-color: #c11815;
  margin-top: 20px;
}

.radio-btn {
  width: 50%;
  float: left;
}

.dialog-footer {
  margin-top: 20px;
}
</style>
