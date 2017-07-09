<template>
  <div class="register-view">
    <template v-if="isComplete">
      <h1 class="title">注册成功</h1>
      <form method="post" onsubmit="return false">
        <p class="tip">请复制以下Token进行登录</p>
        <div class="form-alias">
          <label>
            <strong>token</strong>
            <input
              v-model="token"
              type="text"
              name="token"
              placeholder="token">
          </label>
        </div>
        <div class="form-submit">
          <router-link class="submit" :to="{ name: 'LoginView' }" tag="button">
            去登陆
          </router-link>
        </div>
      </form>
    </template>
    <template v-else>
      <h1 class="title">欢迎加入豆瓣</h1>
      <form method="post" @submit.prevent="onSubmit()">
        <p class="tip error" v-if="error">{{error}}</p>
        <div class="form-alias">
          <label>
            <strong>邮箱</strong>
            <input
              v-model.trim="email"
              type="text"
              name="email"
              placeholder="邮箱">
          </label>
        </div>
        <div class="form-pwd">
          <label>
            <strong>请输入密码</strong>
            <template v-if="passType === 'password'">
              <input
                v-model.trim="pass"
                type="password"
                name="pass"
                placeholder="密码">
            </template>
            <template v-if="passType === 'text'">
              <input
                v-model.trim="pass"
                type="text"
                name="pass"
                placeholder="密码">
            </template>
            <span class="show-pwd" :class="{show: isShow}" @click="showPwd()"></span>
          </label>
        </div>
        <div class="form-name">
          <label>
            <strong>用户名</strong>
            <input
              v-model.trim="name"
              type="text"
              name="name"
              placeholder="用户名">
          </label>
        </div>
        <div class="form-submit">
          <button
            :class="{disabled: isDisabled}"
            :disabled="isDisabled"
            type="submit"
            class="submit">{{registerState}}</button>
        </div>
      </form>
      <div class="footer">
        <div class="agreement">点击「注册」代表你已阅读并同意用户使用协议</div>
        <div class="btns">
          <a href="#">下载豆瓣APP</a>
        </div>
      </div>
    </template>
  </div>
</template>

<script>
  export default {
    name: 'register-view',
    data () {
      return {
        isComplete: false,    // Registration Completed
        isDisabled: false,    // Disabled submit button
        isShow: 0,             // Show pwd
        registerState: '立即注册',
        passType: 'password',    // Password input type
        error: '',                // Verification results
        email: '',
        pass: '',
        name: '',
        token: ''
      }
    },
    methods: {
      showPwd: function () {
        this.isShow = this.isShow ? 1 : 0
        this.isShow ? this.passType = 'text' : this.passType = 'password'
      },
      beforeSubmit: function () {
        // Console.log('Submiting...')
        this.isDisabled = true
        this.registerState = '正在提交'
      },
      onSuccess: function (res) {
        // Console.log('complete!')
        this.isComplete = true
        this.token = res.body.token
      },
      onError: function (err) {
        this.error = err.body.error
        this.registerState = '立即注册'
        this.isDisabled = false
      },
      onSubmit: function () {
        // Disabled submit button
        this.beforeSubmit()
        // Regist...
        this.$store.dispatch({
          type: 'register',
          email: this.email,
          pass: this.pass,
          name: this.name
        }).then(res => {
          // Success handle
          this.onSuccess(res)
        }, err => {
          // Error handle
          this.onError(err)
        })
      }
    },
    // Checkout current user
    beforeRouteEnter (to, form, next) {
      next(vm => {
        if (vm.$store.getters.currentUser.email) {
          // next({path: '/' })
          vm.$router.push({name: 'StatusView'})
        } else {
          next()
        }
      })
    }
  }
</script>
<style lang="scss" scoped>
  .register-view {
    h1 {
      margin: 10% 0 9%;
      font-size: 4rem;
      font-weight: 300;
      color: #42bd56;
      text-align: center;
    }

    form {
      padding: 2rem 1.5rem;

      strong {
        font-size: 1.5rem;
        color: #222222;
        display: none;
        margin-bottom: 0.5rem;
      }

      input::placeholder {
        color: #ccc;
      }

      input[type = 'text'], input[type = 'password'] {
        display: inline-block;
        width: 100%;
        height: 4.4rem;
        padding: 0 0.8rem;
        box-sizing: border-box;
        font-size: 1.5rem;
        background: #ffffff;
        border: 0.1rem solid #ccc;
        border-top-left-radius: 0.3rem;
        border-top-right-radius: 0.3rem;
        outline: 0;
      }

      .form-pwd input, .form-name input {
        padding-right: 4rem;
        border-top: 0;
      }

      .form-pwd {
        position: relative;

        .show-pwd {
          position: absolute;
          right: 0.2rem;
          top: 0;
          display: block;
          height: 100%;
          width: 3.2rem;
          background-image: url("../assets/passopen.png");
          background-repeat: no-repeat;
          background-position: center center;
          background-size: 2.2rem;
          z-index: 3;
        }

        .show-pwd.show {
          background-image: url("../assets/password.png");
        }
      }

      .submit {
        cursor: pointer;
        width: 100%;
        padding: 1.2rem 2.6rem;
        margin-top: 1rem;
        font-size: 1.7rem;
        text-align: center;
        color: #fff;
        background: #42bd56;
        border: 0.1rem solid #17aa52;
        border-radius: 0.3rem;
      }

      .disabled {
        cursor: not-allowed;
        background: #eee;
        border: none;
      }

      .tip {
        font-size: 1.4rem;
        color: #aaa;
      }

      .error {
        color: #ff0000;
      }
    }

    .footer {
      .agreement {
        font-size: 1.4rem;
        color: #aaa;
        text-align: center;
      }

      .btns {
        margin-top: 4rem;
        text-align: center;
        font-size: 1.5rem;

        a {
          color: #42bd56;
          margin-right: 1.5rem;
        }
      }
    }
  }
</style>
