<template>
  <div id="login">
    <el-row type="flex" justify="center" align="middle">
      <el-col :span="12">
        <div>
          <el-container>
            <el-header></el-header>
            <el-main>
              <el-card>
                <el-row type="flex" justify="center" align="middle">
                  <el-form :model="loginForm" ref="loginForm" label-width="80px" :label-position="labelPosition" status-icon>
                  </br>
                  <el-row type="flex" justify="center" align="middle">
                    <img class="logo" src="../assets/logo.png">
                  </el-row>
                  </br>
                    <el-form-item
                      prop="uid"
                      label="UID"
                      :rules="[
                      { required: true, message: '请输入UID', trigger: 'blur' }
                      ]"
                    >
                      <el-input v-model="loginForm.uid"></el-input>
                   </el-form-item>
                    <el-form-item
                      prop="password"
                      label="密码"
                      :rules="[
                      { required: true, message: '请输入密码', trigger: 'blur' }
                      ]"
                    >
                      <el-input type="password" v-model="loginForm.password"></el-input>
                   </el-form-item>
                    </br>
                <el-row type="flex" justify="center" align="middle">
                  <el-button type="success" @click="loginSub" icon="el-icon-arrow-right">登录</el-button> 
                </el-row>
                <el-row type="flex" justify="center" align="middle">
                  <el-button type="text" @click="getUID">如何获取UID</el-button> 
                </el-row>
                  </el-form>
                </el-row>              
              </el-card>
            </el-main>
            <el-footer>
              <a class="copyright">{{ year }} <i class="fa fa-copyright"></i> 南工社联 with <i class="fa fa-heart"></i></a>
            </el-footer>
          </el-container>
        </div>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import QRCode from 'qrcodejs2'
// 引入二维码生成插件

export default {
  data () {
    return {
      labelPosition: 'left',
      loginForm: {
        uid: '',
        password: ''
      },
      year: ''
    }
  },
  name: 'login',
  components: { QRCode },
  mounted () {
    const loading = this.$loading({
      lock: true,
      text: '拼命加载中~'
    })
    setTimeout(() => {
      loading.close()
    }, 2000)
    var date = new Date()
    this.year = date.getFullYear()
  },
  methods: {
    // 打开外部链接
    open (link) {
      this.$electron.shell.openExternal(link)
    },
    // 弹出通知框
    notic (msg, type) {
      this.$notify({
        title: '提示',
        message: msg,
        position: 'bottom-right',
        type: type
      })
    },
    // 生成二维码
    getQr () {
      var str = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789+-='
      var n = 12
      var s = ''
      for (var i = 0; i < n; i++) {
        var rand = Math.floor(Math.random() * str.length)
        s += str.charAt(rand)
      }
      this.$set(this.$data, 'sessionid', s)
      var qrcode = new QRCode(document.getElementById('qrcode'), {
        width: 250,
        height: 250
      })
      qrcode.makeCode(s)
    },
    // 重新加载
    refresh: function () {
      location.reload()
    },
    // 登录提交
    loginSub () {
      console.log(this.loginForm.xm)
      var msg = ''
      var _this = this
      this.$http.get('https://api.oggg.me/stcx')
        .then(function (res) {
          console.log(res.data[0])
          msg = res.data[0]
          _this.notic(msg, 'success')
        })
    },
    // 提示获取UID
    getUID () {
      this.notic('请打开微信小程序，获取登录信息后，您昵称下的UID即为登录所需UID！', 'info')
    }
  }
}
</script>

<style>
/*
CSS
DefaultColor: #409EFF
LogoColor: #2891C8
RecommendRed: #FA5555
*/
body {
  background-color: #FAFAFB;
  background-image: url('../assets/bg.jpg');
  background-repeat: no-repeat;
  background-size: 150% 150%
}

.el-form-item.is-required .el-form-item__label:before {
    content: ' ';
    color: #fa5555;
}

.logo{
  width: 20%;
  height: 20%;
}

.copyright{
  color: #fff;
  font-size: 14px;
}
</style>