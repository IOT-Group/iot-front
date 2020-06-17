<template>
  <div class="row container-login">
    <div class="card col-3 container-form">
      <h5><b>登入智能家居系统</b></h5>    
      <input type="text" class="form-control" v-model="loginForm.username" placeholder="请输入用户名">
      <input type="password" class="form-control" v-model="loginForm.password" placeholder="请输入密码">
      <button v-on:click="login" class="btn btn-success">登录</button>
    </div>
    <wave class="animation-wave"></wave>
  </div>
</template>

<script>
  import wave from './animations/wave'

  export default {
    name: 'Login',
    data () {
      return {
        loginForm: {
          username: '',
          password: ''
        },
        responseResult: []
      }
    },
    methods: {
      login () {
        this.$axios
          .post('/login', {
            username: this.loginForm.username,
            password: this.loginForm.password
          })
          .then(successResponse => {
            if (successResponse.data.code === 200) {
              this.$router.replace({path: '/index'})
            }
          })
          .catch(failResponse => {
            console.error(failResponse)
          })
      }
    },components:{
      wave
    }
  }
</script>

<style>
  .container-login{
    justify-content: center;
  }

  .container-form{
    border-radius: .5rem;
    padding: 3rem 2.5rem 3rem 2.5rem;
    width: 15rem;
    height: 25rem;
    justify-content: space-around;
  }

  .animation-wave{
    position: fixed;
    bottom: 0;
  }
</style>