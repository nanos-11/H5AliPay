<template>
  <div class="pay-page">
    <div class="pay-scroll">
      <div class="virtual">
        <div class="rest">
          <div class="rest-content">50人</div>
          <div class="rest-txt">VIP社群剩余名额</div>
        </div>
        <div class="rest">
          <div class="rest-content">
            {{minute}}:{{second}}
          </div>
          <div class="rest-txt">剩余支付时间</div>
        </div>
      </div>
      <div class="gap"></div>
      <div class="verify-tel">
        <div class="verify-title">验证手机</div>
        <div class="tel-space">
          <div class="tel-icon">
            <img src="../../static/images/tel-icon.png">
          </div>
          <input type="tel" placeholder="手机号" oninput="value=value.replace(/[^\d]/g,'')" maxlength="11" v-model="phone">
        </div> <!---->
        <div :v-model="isShow" style="font-size: 0.34rem; color: red;margin-left: 0.8rem;">{{message}}</div>
        <div class="code-space">
          <div class="input-space">
            <div class="code-icon"><img src="../../static/images/code-icon.png"></div>
            <input type="number" placeholder="验证码" oninput="value=value.replace(/[^\d]/g,'')" maxlength="6"
                   v-model="code"></div>
          <div class="tiktok" @click="getCode" v-show="show">获取验证码</div> <!---->
          <div v-show="!show" class="tiktok" style="color:#ffffff;">{{count}}秒</div>
        </div>
        <div :v-model="isShow" style="font-size: 0.34rem; color: red;margin-left: 0.8rem;">{{codeMessage}}</div>
      </div>
      <div class="gap"></div>
      <div class="course-info">
        <div class="course-title">课程信息</div>
        <div class="course-desc">21天亲子沟通训练营 (理论基础+育儿技巧+真实案例)</div>
        <div class="course-price">
          <div class="money-icon">￥</div>
          <div class="money">9.9</div>
        </div>
        <div class="line"></div>
        <div class="extra">
          <img src="../../static/images/kebiao.jpg" alt="" width="100%">
        </div>
      </div>
    </div>
    <div class="pay-btn">
      <div class="pay-left">
        <div class="money-space">
          <div class="money-icon">￥</div>
          <div class="money">9.9</div>
        </div>
      </div>
      <button class="pay-right" @click="loginPhone" :disabled="isPay">{{isPayMessage}}</button>
    </div>
  </div>
</template>

<script>
  import "../../static/js/cover.min.js"
  import {isPay, getBindVerCode, loginPhone, createAliPay, getPhoneData} from "../api/home";
  import {track} from "../utils/util.js"
  
  /**
   * 验证手机号是否正确
   *
   * @date 2019/6/21
   * @author nan
   */
  function verifyPhone() {
    if (this.phone === '') {
      this.message = "请输入手机号";
      this.isShow = true;
      return false
    }
    let pattern = /^1[23456789]\d{9}$/
    if (pattern.test(this.phone) !== true) {
      this.message = "请输入正确的手机号";
      this.isShow = true;
      return false
    }
    return true
  }
  
  /**
   * 验证验证码是否正确
   *
   * @date 2019/6/21
   * @author nan
   */
  function verifyCode() {
    if (this.code === '') {
      this.codeMessage = "请输入验证码";
      this.isShow = true;
      return false
    }
    return true
  }
  
  /**
   * 倒计时60秒
   *
   * @date 2019/6/28
   * @author nan
   */
  function timerCode() {
    // 定义60秒的时间
    const TIME_COUNT = 60
    // 当timer不为空时，显示倒计时
    if (!this.timer) {
      this.count = TIME_COUNT
      this.show = false
      this.timer = setInterval(() => {
        // count从60开始--,到0时清除倒计时，隐藏倒计时，显示文本
        if (this.count > 0 && this.count <= TIME_COUNT) {
          this.count--
        } else {
          this.show = true
          clearInterval(this.timer)
          this.timer = null
        }
      }, 1000)
    }
  }
  
  export default {
    name: "RegisterPhone",
    data() {
      return {
        minutes: 30,
        seconds: 0,
        address: '',//地址
        message: '',  //提示信息
        codeMessage: '',
        isShow: '',  //是否显示提示信息
        // 获取验证码显示
        show: true,
        // 验证码倒计时
        count: '',
        // 时间默认为null
        timer: null,
        
        phone: '',
        code: '',
        /*dialog: {
          isShow: false,
          isLoadingShow: false,
          message: ""
        },*/
        isPay: false,
        isPayMessage: '确认支付'
      };
    },
    watch: {
      second: {
        handler(newVal) {
          this.num(newVal)
        }
      },
      minute: {
        handler(newVal) {
          this.num(newVal)
        }
      }
    },
    // components: {confirm},
    computed: {
      second: function () {
        return this.num(this.seconds)
      },
      minute: function () {
        return this.num(this.minutes)
      }
    },
    mounted() {
      window.loginPhone = this.loginPhone
    },
    /**
     * 验证手机号是否 支付过
     *
     * @date 2019/6/28
     * @author nan
     */
    created() {
      let phone = localStorage.getItem('phone');
      isPay(phone, 1).then(res => {
        this.isPay = res.status
        this.isPayMessage = res.status ? '已支付' : '确认支付';
      })
      this.getAddress()
    },
    methods: {
      add: function () {
        let _this = this;
        let time = window.setInterval(function () {
          if (_this.seconds === 0 && _this.minutes !== 0) {
            _this.seconds = 59;
            _this.minutes -= 1;
          } else if (_this.minutes === 0 && _this.seconds === 0) {
            _this.seconds = 0;
            window.clearInterval(time);
          } else {
            _this.seconds -= 1;
          }
        }, 1000);
      },
      num: function (n) {
        return n < 10 ? "0" + n : "" + n
      },
      /**
       * 获取地址信息
       *
       * @date 2019/7/1
       * @author nan
       */
      getAddress() {
        let cip = window.returnCitySN.cip;
        const KEY = 'DOFBZ-AVFE2-KVAUJ-C4GP3-V4IJ2-GPFAY'; //key 秘钥自己申请
        let url = 'https://apis.map.qq.com/ws/location/v1/ip?ip=' + cip + '&key=' + KEY;
        this.$jsonp(url, {
          callbackName: 'QQmap',
          output: 'jsonp',
        })
        .then(json => {
          // adcode: 110105
          // city: "北京市"
          // district: "朝阳区"
          // nation: "中国"
          // province: "北京市"
          this.address = json.result.ad_info.city;
        })
        .catch(err => {
          console.log(err)
        })
      },
      /**
       * 获取验证码
       *
       * @date 2019/6/21
       * @author nan
       */
      getCode() {
        this.message = ''
        // 验证手机号是否正确
        if (!verifyPhone.call(this)) return
        // 验证码计时器
        timerCode.call(this)
        // 倒计时
        // console.log('nan getCode', this.minutes)
        if (this.minutes === 30) {
          if (!this.isPay) {
            this.add()
          }
        }
        // 获取验证码接口
        getBindVerCode(this.phone).then(res => {
          // console.log('nan ', res)
          if (res.errorCode === 1005) {
            this.message = "验证码发送频繁，请稍候再试"
          }
        }, error => {
        
        })
        // 获取手机号给螳螂
        getPhoneData(this.phone, this.address).then(res => {
        })
      },
      /**
       * 根据手机号登录或者注册
       * @date 2019/6/19
       * @author nan
       */
      loginPhone() {
        // 验证手机号是否正确
        if (!verifyPhone.call(this)) return
        // 验证验证码是否正确
        if (!verifyCode.call(this)) return
        // 保存手机号
        localStorage.setItem('phone', this.phone)
        // 登录注册接口
        loginPhone(this.phone, this.code, 1).then(res => {
          // 开始支付
          // console.log('nan re', res)
          if (res === undefined) {
            return
          }
          if (res.errorCode === 1006) {
            this.codeMessage = "验证码错误";
            return
          }
          if (res.pay_type) {
            this.isPay = true
            this.isPayMessage = '已支付'
            return
          }
          this.startAliPay();
        })
      },
      /**
       * 设置支付宝支付参数
       *
       * @date 2019/6/21
       * @author nan
       */
      startAliPay() {
        meteor.track('form', {convert_id: 1643737926516749 })
        this.getAliOrder();
      },
      /**
       * 绑定订单
       *
       * @date 2019/7/1
       * @author nan
       */
      getAliOrder() {
        // 绑定订单
        // 参数  'phone', 'subject', 'quitUrl','course_id' course_id  值为1
        let params = {
          'phone': this.phone,
          'subject': '21天亲子沟通训练营 (理论基础+育儿技巧+真实案例)',
          'course_id': 1
        }
        createAliPay(params).then(res => {
          if (res === undefined) {
            return
          }
          const div = document.createElement('div');
          div.innerHTML = res;
          document.body.appendChild(div);
          document.forms[0].submit();
        }, error => {
          // console.log('nan 绑定订单失败', error)
          window.alert("绑定订单失败")
        })
      },
  
      startWechatPay(money) {
        let orderData = this.getWechatOrder(money);
        unifiedWechat(orderData).then(res => {
          let webUrl = res.webUrl;
          if (webUrl) {
            this.dialog.isLoadingShow = false;
            window.location.href = webUrl;
            return;
          }
        });
      },
      //生成订单
      getWechatOrder(money) {
        let orderNum = this.getOrderNumber();
        // 验证码
        let veriCode = "";
        for (let i = 0; i < 4; i++) {
          veriCode += Math.floor(Math.random() * 10);
        }
        let sceneinfo = {
          h5_info: {
            type: "Wap",
            wap_url: window.location.href,
            wap_name: document.title
          }
        };
        let order = {
          backUrl:'http://test.quick.ipay.so',
          busicd: "WAPP",
          charset: "utf-8",
          chcd: "WXP",
          inscd: '10130001',
          mchntid: this.merID,
          orderNum: orderNum,
          signType: "SHA256",
          terminalid: "10000001",
          txamt: '9.9￥',
          mchntIP: this.params.mchntIP,
          txndir: "Q",
          version: "2.3.2",
          sceneinfo: JSON.stringify(sceneinfo),
        };
        let sign = getSign(order);
        order.sign = sign;
        return order;
      },
  
      // 生成订单号
      getOrderNumber() {
        let dateStr = this.formatDate(new Date());
        let veriCode = "";
        for (let i = 0; i < 5; i++) {
          veriCode += Math.floor(Math.random() * 10).toString();
        }
        return dateStr + veriCode;
      },
  
      // 格式化金额
      formatMoney(money) {
        money = Math.round(money * 100).toString();
        while (money.length < 12) {
          money = "0" + money;
        }
        return money;
      },
      // 格式化日期
      formatDate(date) {
        if (!(date instanceof Date)) {
          return "";
        }
        let year =
          date.getFullYear() % 100 > 9
            ? (date.getFullYear() % 100).toString()
            : 0 + date.getFullYear % 100;
        let month =
          date.getMonth() > 8
            ? (date.getMonth() + 1).toString()
            : "0" + (date.getMonth() + 1).toString();
        let day =
          date.getDate() > 9
            ? date.getDate().toString()
            : "0" + date.getDate().toString();
        let hour =
          date.getHours() > 9
            ? date.getHours().toString()
            : "0" + date.getHours().toString();
        let minute =
          date.getMinutes() > 9
            ? date.getMinutes().toString()
            : "0" + date.getMinutes().toString();
        let second =
          date.getSeconds() > 9
            ? date.getSeconds().toString()
            : "0" + date.getSeconds().toString();
        return year + month + day + hour + minute + second;
      }
    }
  };
</script>

<style scoped lang="css">
  @import "../../static/css/chunkd.css";
  @import "../../static/css/pay.css";
  
  /*.dialog-wrapper .container{
  @include wh(60%,24%);
    background: #fff;
    z-index: 1;
  }
  .dialog-wrapper .container p{
    line-height: 20px;
    margin: 38px 0 20px 0;
    text-align: center;
    min-height: 20px;
  }
  .dialog-wrapper .container div.confirm {
    line-height: 40px;
    margin-top: 40px;
  }
  .dialog-wrapper .container button {
    display: inline-block;
    @include wh(60px, 30px);
  }
  .dialog-wrapper .container button:nth-of-type(1) {
    margin: 0 20px 0 36px;
  }*/
</style>
