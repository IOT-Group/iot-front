<template>
  <div class="row container-login">
    <div class="card col-3 container-form">
      <div class="container-form-header">
        <h5><b>{{isLoginWindow?'登录':'注册'}}智能家居系统</b></h5>
        <a href="#" @click="isLoginWindow=!isLoginWindow" style="position:absolute;right:2rem">{{isLoginWindow?'注册':'登陆'}}</a>
      </div>
      <div class="alert alert-danger" v-if="isAlert">{{alertMessage}}</div>
      <template v-if="isLoginWindow">
        <input type="text" class="form-control" v-model="loginForm.username" placeholder="请输入用户名">
        <input type="password" class="form-control" v-model="loginForm.password" placeholder="请输入密码">
        <button  v-on:click="login" class="btn btn-success">登录</button>
      </template>
      <template v-else>
        <input type="text" class="form-control" v-model="registerForm.username" placeholder="请输入用户名">
        <input type="password" class="form-control" v-model="registerForm.password" placeholder="请输入密码">
        <button  v-on:click="register" class="btn btn-success">注册</button>
      </template>
    </div>
    <wave class="animation-wave"></wave>
  </div>
</template>

<script>
  import wave from './animations/Wave'  

  export default {
    name: 'Login',
    data () {
      return {
        isLoginWindow: true,
        isAlert:false,
        alertMessage:'',
        loginForm: {
          username: '',
          password: ''
        },
        registerForm:{
          username:'',
          password:''
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
            }else{
              this.isAlert=true
              alertMessage='登录失败'
            }
          })
          .catch(failResponse => {
            console.error(failResponse)
          })
      },
      regitster(){
        this.$axios.post('/register',{
          username: this.registerForm.username,
          password: this.registerForm.password
        }).then(succ=>{
          if(succ.data.code===200){
            
          }else{
            this.isAlert=true
            alertMessage='登录失败'
          }
        })
      }
    },components:{
      wave
    }
  }
</script>

<style scoped>
  .container-login{
    justify-content: center;
    margin-top: 10rem;
  }

  .container-form{
    border-radius: .5rem;
    padding: 3rem 2.5rem 3rem 2.5rem;
    width: 15rem;
    height: 25rem;
    justify-content: space-around;
  }

  .container-form-header{
    display: flex;
    justify-content: center;
  }

  .animation-wave{
    position: fixed;
    bottom: 0;
  }
</style>