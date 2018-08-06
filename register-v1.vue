<template>
  <div class="register1-page">
    <p class="first-level">欢迎注册1Token.trade</p>
    <div class="container">
      <!-- Page Title -->
      <div class="captial">
        <div 
          v-show="switchPage === 'register'" 
          class="captial-title">
          <span 
            v-show="phoneOrEmail === 'phone'" 
            class="title">手机号注册</span>
          <span 
            v-show="phoneOrEmail === 'email'" 
            class="title">邮箱注册</span>
          <a 
            v-show="phoneOrEmail === 'phone'"
            class="linkto"
            @click="toggleRegis('email')">邮箱注册</a>
          <a 
            v-show="phoneOrEmail === 'email'"
            class="linkto"
            @click="toggleRegis('phone')">手机号注册</a>
        </div>

        <div 
          v-show="switchPage === 'account'" 
          class="captial-title">
          <span class="title">开通交易所账户</span>
        </div>

        <div 
          v-show="switchPage === 'complete'" 
          class="captial-title">
          <span class="title">注册成功</span>
        </div>
      </div>
      
      <!-- Common Steps -->
      <ul class="lines">
        <li class="line active"><span>1</span><a href="###">注册账户</a></li>
        <li class="line"><span>2</span><a href="">开通账户</a></li>
        <li class="line"><span>3</span><a href="">注册完成</a></li>
      </ul>

      <div class="lists">
        <div 
          v-show="switchPage === 'register'" 
          class="register-box">
          <div class="form-control">
            <div 
              v-show="phoneOrEmail === 'phone'" 
              class="form-control-phone" >
              <input
                v-model="regisData.areaCode"
                class="other-width"
                style="cursor: pointer;" 
                type="text"
                readonly
                @click="handleShowAreaList"
                @blur="validateField('areaCode')">
              <span 
                v-if="tips.areaCode"
                class="tips error">请选择国家代码</span>
              <input
                v-model="regisData.phone"
                class="another-width"
                type="text" 
                placeholder="手机号"
                @blur="validateField('phone')">
              <span 
                v-if="tips.phone.show"
                class="tips error phone-tips">{{ tips.phone.tip }}</span>
            </div>

            <div 
              v-show="phoneOrEmail === 'email'" 
              class="form-control-email" >
              <input
                v-model="regisData.email"
                type="text" 
                placeholder="邮箱"
                @blur="validateField('email')">
              <span 
                v-if="tips.email.show"
                class="tips error">{{ tips.email.tip }}</span>
            </div>

            <div 
              v-show="areaListIsShow === true" 
              class="timeZone">
              <ul class="zone-head">
                <li 
                  v-for="(_, group) in areaCodeList"
                  :class="{active: currGroup === group}" 
                  :key="group"
                  @click="toggleTab(group)" >{{ group.toUpperCase() }}</li>
              </ul>
              <ul class="zone-content">
                <li 
                  v-for="(country, index) in currGroupCountrys" 
                  :key="country['area_code']"
                  @click="selectCountry(index)">
                  <!-- {{ currGroupCountrys[area] }} -->
                  <span>
                    {{ country['name_cn'] + ' +' + country['area_code'] }}
                  </span>
                </li>
                <!-- <li>印度尼西亚 +62</li>
                <li>澳门 +853</li> -->
              </ul>
            </div>
          </div>        
          <div class="form-control">
            <input 
              :placeholder="phoneOrEmail === 'phone' ? '短信验证码' : '邮箱验证码'"
              v-model="regisData.code"
              class="another-width" 
              type="text"
              @blur="validateField('code')">
            <span 
              v-if="tips.code.show"
              class="tips error">{{ tips.code.tip }}</span>
            <button
              v-if="!showCountDown"
              id="TencentCaptchaRegister" 
              class="other-width getcode">{{ phoneOrEmail === 'phone' ? '获取短信验证码' : '获取邮箱验证码' }}</button>
            <span 
              v-else
              class="other-width countdown">{{ countdown }}秒后重新获取</span>
          </div>
          <div class="form-control">
            <input
              v-model="regisData.username"
              placeholder="用户名以字母开头，可用小写字母，数字，符号’-‘"
              type="text"
              @blur="validateField('username')">
            <span
              v-show="tips.username.show"
              :class="{error: tips.username.error}"
              class="tips" >{{ tips.username.tip }}</span>
          </div>
          <div class="form-control"><input
            v-model="pass1"
            type="password"
            placeholder="请设置密码"
            @blur="validateField('pass1')">
            <span
              v-show="tips.pass1.show"
              class="tips error">{{ tips.pass1.tip }}</span>
          </div>
          <div class="form-control"><input
            v-model="pass2"
            type="password" 
            placeholder="请再次输入密码"
            @blur="validateField('pass2')">
            <span
              v-show="tips.pass2.show"
              class="tips error">{{ tips.pass2.tip }}</span>
          </div>
          <div class="form-control">
            <label class="check-box unselectable">
              <input
                v-model="agreement"
                type="checkbox">
              <span class="indicator"/>
            </label>
            <span class="agree">我已同意并阅读 <a href="###">用户协议</a></span>
          </div>
          <div 
            :class="{loading: isloading === true}" 
            class="btn-group"
            @click="register">
            <a
              class="btn">{{ isloading === true ? '正在注册' : '注册' }}</a>
            <span
              v-show="tips.register.show"
              class="register-tip">{{ tips.register.tip }}</span>
          </div>
        </div>

        <div 
          v-show="switchPage === 'account'" 
          class="openAccount-box">
          <div class="head-tips">
            <span class="const">开通交易所账户</span>
            <span class="what-text">什么是统一账户<img 
              :src="`${resourceRoot}/web/register/why-icon.png`" 
              alt=""></span>
          </div>
          <div class="choose-exchange">
            <div class="item">
              <label class="check-box unselectable">
                <input 
                  v-model="accounts.huobi" 
                  type="checkbox">
                <span class="indicator"/>
                <img 
                  :src="`${resourceRoot}/web/register/huobi-icon.png`" 
                  alt="">
              </label>       
              <span class="account-name">火币统一账户名</span>
            </div>
            <input
              v-model="exchanges.huobi.value" 
              type="text">
            <span
              v-show="exchanges.huobi.show"
              class="acc-tips">请填写账户名</span>
          </div>
          <div class="choose-exchange">
            <div class="item">
              <label class="check-box unselectable">
                <input 
                  v-model="accounts.okex"
                  type="checkbox">
                <span class="indicator"/>
                <img 
                  :src="`${resourceRoot}/web/register/okex-icon.png`" 
                  alt="">
              </label>
              <span class="account-name">OKEX统一账户名</span>
            </div>
            <input
              v-model="exchanges.okex.value" 
              type="text">
            <span
              v-show="exchanges.okex.show"
              class="acc-tips">请填写账户名</span>
          </div>
          <div class="choose-exchange">
            <div class="item">
              <label class="check-box unselectable">
                <input 
                  v-model="accounts.binance"
                  type="checkbox">
                <span class="indicator"/>
                <img 
                  :src="`${resourceRoot}/web/register/binance-icon.png`" 
                  alt="">
              </label> 
              <span class="account-name">币安统一账户名</span>
            </div>
            <input
              v-model="exchanges.binance.value" 
              type="text">
            <span
              v-show="exchanges.binance.show"
              class="acc-tips">请填写账户名</span>
          </div>
          <div class="choose-exchange">
            <div class="item">
              <label class="check-box unselectable">
                <input 
                  v-model="accounts.gate"
                  type="checkbox">
                <span class="indicator"/>
                <img 
                  :src="`${resourceRoot}/web/register/gate-icon.png`" 
                  alt="">
              </label>
              <span class="account-name">Gate.io统一账户名</span>
            </div>
            <input
              v-model="exchanges.gate.value"
              type="text">
            <span
              v-show="exchanges.gate.show"
              class="acc-tips">请填写账户名</span>
          </div>
          <div class="choose-exchange">
            <div class="item">
              <label class="check-box">
                <input
                  v-model="accounts.fcoin"
                  type="checkbox">
                <span class="indicator"/>
                <img 
                  :src="`${resourceRoot}/web/register/fcoin-icon.png`" 
                  alt="">
              </label>
              <span class="account-name">Fcoin统一账户名</span>
            </div>
            <input
              v-model="exchanges.fcoin.value" 
              type="text">
            <span
              v-show="exchanges.fcoin.show"
              class="acc-tips">请填写账户名</span>
          </div>
          <div class="btn-unique">
            <div 
              class="btn-group"
              @click="addAccount">
              <a
                class="btn">开通</a>
            </div>
            <div class="prompt">
              <span 
                class="jump" 
                @click="togglePage('complete')">跳过，暂不开通</span>
              <span class="jump">(进入用户中心自行开通)</span>
            </div>
            <span 
              v-if="tips.addAccount.show"
              class="add-accounts">{{ tips.addAccount.tip }}</span>
          </div>
        </div>

        <div 
          v-show="switchPage === 'complete'" 
          class="complete-box">
          <img 
            :src="`${resourceRoot}/web/register/complete-icon.png`" 
            alt="">
          <span class="congratulations">恭喜您！已经注册成功</span>
          <div class="btn-group">
            <router-link 
              to="/login" 
              class="btn">去登录</router-link>
          </div>
        </div>
      </div>

    </div>
    <p class="isLogin"><router-link to="/login">登录已有1Token账号  ></router-link></p>
  </div>
</template>

<script>

import { phoneList } from '../settings/index'
import { mapState, mapActions } from 'vuex'
import axios from 'axios'
import md5 from 'js-md5'

export default {
  name: 'Register1',
  data () {
    return {
      phoneOrEmail: 'phone',
      switchPage: 'register',
      isloading: false,

      areaCodeList: {},
      currGroup: '热门',
      areaListIsShow: false,
      regisData: {
        areaCode: '',
        phone: '',
        code: '',
        username: '',
        email: ''
      },
      tips: {
        areaCode: false,
        phone: {
          tip: '',
          show: false
        },
        email: {
          tip: '',
          show: false
        },
        code: {
          tip: '',
          show: false
        },
        username: {
          tip: '',
          show: false,
          error: false
        },
        pass1: {
          tip: '',
          show: false
        },
        pass2: {
          tip: '',
          show: false
        },
        register: {
          tip: '',
          show: false
        },
        addAccount: {
          tip: '',
          show: false
        }
      },
      showCountDown: false,
      countdown: 60,
      humanCode: undefined,
      pass1: '',
      pass2: '',
      accounts: {
        huobi: true,
        okex: true,
        binance: true,
        gate: true,
        fcoin: true
      },
      agreement: true,
      exchanges: {
        huobi: {
          value: '',
          show: false
        },
        okex: {
          value: '',
          show: false
        },
        binance: {
          value: '',
          show: false
        },
        gate: {
          value: '',
          show: false
        },
        fcoin: {
          value: '',
          show: false
        }
      }
    }
  },
  computed:{
    ...mapState(['resourceRoot']),

    currGroupCountrys () {
      return this.areaCodeList[this.currGroup]
    },
    fields () {
      let fields = this.phoneOrEmail === 'phone' ? ['areaCode', 'phone', 'code', 'username', 'pass1', 'pass2'] : ['email', 'code', 'username', 'pass1', 'pass2']
      return fields
    }
  },
  watch: {
  },
  created () {
  },
  mounted () {
    this.areaCodeList = phoneList

    let d = document.getElementById('TencentCaptchaRegister')
	  if(d) {
	  new TencentCaptcha(
        d,
        '2023983860',
        res => {
          if(res.ticket && res.randstr) {
            this.phoneOrEmail === 'phone' ? (this.validateField('areaCode'), this.validateField('phone')) : this.validateField('email')
            if(!this.tips[this.phoneOrEmail].show && !this.tips.areaCode) {
              this.getHumanCode(res.ticket, res.randstr)
            }
          }
        })
	  }
  },
  methods: {
    toggleTab (group) {
      this.currGroup = group
    },
    handleShowAreaList () {
      this.areaListIsShow = !this.areaListIsShow
    },
    selectCountry (idx) {
      this.regisData.areaCode = `${this.currGroupCountrys[idx].name_cn} +${this.currGroupCountrys[idx].area_code}`
      this.areaListIsShow = false
      this.tips.areaCode = false
    },
    validateField (key) {
      var phoneReg = /^\d+$/
      var emailReg = /^[-.\w]+@([\w-]+\.)+[\w-]{2,20}$/
      var usernameReg = /^[a-z][a-z0-9\-]+$/
      switch(key) {
        case 'areaCode':
          this.regisData.areaCode === '' ? this.tips.areaCode = true : this.tips.areaCode = false
          break
        case 'phone':
          if(phoneReg.test(this.regisData.phone)) {
            this.tips.phone.show = false
            // this.tips.phone.tip = ''
          } else {
            this.tips.phone.show = true
            this.regisData.phone === '' ? this.tips.phone.tip = '请输入手机号' : this.tips.phone.tip = '请输入正确的手机号'
          }
          break
        case 'email':
          if(emailReg.test(this.regisData.email)) {
            this.tips.email.show = false
            // this.tips.phone.tip = ''
          } else {
            this.tips.email.show = true
            this.regisData.email === '' ? this.tips.email.tip = '请输入邮箱' : this.tips.email.tip = '请输入正确的邮箱'
          }
          break
        case 'code':
          if(this.regisData.code === '') {
            this.tips.code.show = true
            this.tips.code.tip = '请输入验证码' 
          } else {
            this.tips.code.show = false
            // this.tips.code.tip = '' 
          }
          break
        case 'username':
          this.tips.username.show = true
          if(usernameReg.test(this.regisData.username)) {
            this.tips.username.error = false
            this.tips.username.tip = '用户名注册后不可更改，请谨慎填写'
          } else {
            this.tips.username.error = true
            this.regisData.username === '' ? this.tips.username.tip = '请输入用户名' : this.tips.username.tip = '请输入正确用户名'
          }
          break
        case 'pass1':
          // this.pass1 === '' ? this.tips.pass1 = true : this.tips.pass1 = false
          if(this.pass1 === '') {
            this.tips.pass1.show = true
            this.tips.pass1.tip = '请输入密码'
          } else if(this.pass1.length < 6) {
            this.tips.pass1.show = true
            this.tips.pass1.tip = '密码长度不能少于6位'
          } else {
            this.tips.pass1.show = false
          }
          break
        case 'pass2':
          if(this.pass2 === '') {
            this.tips.pass2.show = true
            this.tips.pass2.tip = '请输入密码'
          } else {
            if(this.pass1 === this.pass2) {
              this.regisData.pass = this.pass1
              this.tips.pass2.show = false
            } else {
              this.tips.pass2.tip = '两次输入的密码不一致，请重新输入'
              this.tips.pass2.show = true
            }
          }
          break
      }
    },
    getCode () {
      if(this.tips.areaCode || this.tips[this.phoneOrEmail].show || this.tips.code.show) {
        return
      }
      let param = {
        type: this.phoneOrEmail,
        human_code: this.humanCode
      }
      param[this.phoneOrEmail] = this.phoneOrEmail === 'phone' ? this.regisData.areaCode.split('+')[1] + '-' + this.regisData.phone : this.regisData.email
      // console.log(param)
      axios.post('/api/v1/user/register', param).then(res => {
        this.showCountDown = true
        let id = setInterval(() => {
          if(this.countdown > 0) {
            --this.countdown
          } else {
            clearInterval(id)
            this.showCountDown = false
            this.countdown = 60
          }
        }, 1000)
      }).catch(err => {
        this.handleError(err, 'code')
      })

    },
    getHumanCode (ticket, randstr) {
      axios.post('/api/v1/user/human-code', { ticket: ticket, randstr: randstr })
        .then((res) => {
          this.tips.code.show = false
          this.humanCode = res.data.human_code
          this.getCode()
        })
        .catch((err) => {
          this.handleError(err, 'code')
        })
    },
    handleError (err, key) {
      this.tips[key].show = true
      setTimeout(() => {
        this.tips[key].show = false
      }, 4000)
      if (err.response && err.response.data && err.response.data.message) {
        //this.showMessage(err.response.data.message)
        this.tips[key].tip = err.response.data.message
      } else {
        //this.showMessage('未知错误')
        this.tips[key].tip = '未知错误'
      }
    },
    register () {
      this.fields.forEach(f => {
        this.validateField(f)
      })
      if(this.tips.areaCode || this.tips[this.phoneOrEmail].show || this.tips.code.show || this.tips.username.error || this.tips.pass1 || this.tips.pass2.show || this.agreement === false) {
        this.tips.register.show = true
        this.agreement === false ? this.tips.register.tip = '请先同意用户协议' : this.tips.register.tip = '您输入的信息有误'
        setTimeout(() => {
          this.tips.register.show = false
        }, 4000)
        return
      }
      let param = {
        human_code: this.humanCode,
        type: this.phoneOrEmail,
        username: this.regisData.username
      }
      param[this.phoneOrEmail] = this.regisData.areaCode.split('+')[1] + '-' + this.regisData.phone
      axios.post('/api/v1/user/register-check', param)
        .then(res => {
          this.setPass(param)
        })
        .catch(err => {
          // this.tips.register.show = true
          // if (err.response && err.response.data && err.response.data.message) {
          //   // this.showMessage(err.response.data.message)
          //   this.tips.register.tip = err.response.data.message
          // } else {
          //   this.tips.register.tip = '未知错误'
          //   // this.showMessage('未知错误')
          // }
          // setTimeout(() => {
          //   this.tips.register.show = false
          // }, 3000)
          this.handleError(err, 'register')
        })
    },
    randomString (len) {
      len = len || 32
      let $chars = 'ABCDEFGHJKMNPQRSTWXYZabcdefhijkmnprstwxyz2345678'　　
      let maxPos = $chars.length　　
      let rdStr = ''　　
      for (let i = 0; i < len; i++) {　　　　
        rdStr += $chars.charAt(Math.floor(Math.random() * maxPos))　　
      }
      return rdStr
    },
    setPass (param) {
      let salt = this.randomString(8)
      param.salt = salt
      param.code = this.regisData.code
      param.password = md5(this.regisData.pass + salt)
      axios.post('/api/v1/user/register-set-password', param)
        .then(res => {
          this.togglePage('account')
        })
        .catch(err => {
          // this.tips.register.show = true
          // if (err.response && err.response.data && err.response.data.message) {
          //   // this.showMessage(err.response.data.message)
          //   this.tips.register.tip = err.response.data.message
          // } else {
          //   this.tips.register.tip = '未知错误'
          //   // this.showMessage('未知错误')
          // }
          // setTimeout(() => {
          //   this.tips.register.show = false
          // }, 3000)
          this.handleError(err, 'register')
        })
    },
    togglePage (page) {
      this.switchPage = page
    },
    addAccount () {
      let selectedAccs = []
      let hasAccName = true
      for(let account in this.accounts) {
        if(this.accounts[account]) {
          selectedAccs.push(account)
        }
      }
      for(let selectedAcc of selectedAccs) {
        if(this.exchanges[selectedAcc].value === '') {
          this.exchanges[selectedAcc].show = true
          hasAccName = false
        }
        setTimeout(() => {
          this.exchanges[selectedAcc].show = false
        }, 4000)
      }
      if(!hasAccName) {
        return
      }
      let reqs = []
      selectedAccs.forEach(ex => {
        let param = {
          account: this.exchanges[ex].value,
          exchange: ex
        }
        reqs.push(axios.post('/api/v1/user/add-account', param))
      })
      Promise.all(reqs).then(res => {
        this.togglePage('complete')
      }).catch(err => {
        // this.tips.addAccount.show = true
        // if (err.response && err.response.data && err.response.data.message) {
        //   // this.showMessage(err.response.data.message)
        //   this.tips.addAccount.tip = err.response.data.message
        // } else {
        //   this.tips.addAccount.tip = '未知错误'
        //   // this.showMessage('未知错误')
        // }
        // setTimeout(() => {
        //   this.tips.addAccount.show = false
        // }, 3000)
        this.handleError(err, 'addAccount')
      })
    },
    toggleRegis (type) {
      this.phoneOrEmail = type
    }
  }
}
</script>

<style scoped lang="scss" src="../assets/style/views/register1.scss"></style>
