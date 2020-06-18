<template>
  <div>
    <!-- 导航栏 -->
    <div class="container-navbar">
      <nav class="navbar navbar-expand-lg navbar-light bg-light">
        <span class="navbar-brand">HOME</span>
        <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbar-content">
          <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbar-content">
          <ul class="navbar-nav">
            <li class="nav-item" v-bind:class="{active:environmentconsole}">
              <a data-toggle="collapse" href="#collapse-environment" class="nav-link" v-on:click="environmentconsole=!environmentconsole">环境管理</a>
            </li>
            <li class="nav-item">
              <a href="#" class="nav-link" data-toggle="modal" data-target="#modal-schedule">日程管理</a>
            </li>
            <li class="nav-item">
              <a href="#" class="nav-link" data-toggle="modal" data-target="#modal-device">添加设备</a>
            </li>
          </ul>
        </div>
        <div v-show="isAlert" class="alert alert-success" style="margin-bottom:0;padding:.3rem 1.25rem;margin-right:3rem">{{alertMessage}}</div>
        <div v-show="isVoiceAlert" class="alert alert-success" style="margin-bottom:0;padding:.3rem 1.25rem;margin-right:3rem">{{voiceAlertMessage}}</div>
        <div class="navbar-right">
          <div class="input-group">
            <div class="input-group-prepend">
              <span class="input-group-text">语音控制</span>
            </div>
            <input type="text" class="form-control" placeholder="请输入想要发送的语音信息" v-model="voiceMessage">
          </div>
          <button class="btn btn-success" style="margin-left:2rem" @click="sendVoiceMessage">发送</button>
        </div>
      </nav>
    </div>

    <!-- 主容器 -->
    <div class="container-fluid container-main">
      <!-- 环境控制台 -->
      <div class="collapse card console-environment" id="collapse-environment">
        <div class="console-environment-header">
          <h4>环境控制台</h4>
          <button :class="['btn-sm btn btn-ownerstate',env.ownerstate?'btn-secondary':'btn-success']" @click="env.ownerstate=!env.ownerstate">{{env.ownerstate?'出门':'回家'}}</button>
        </div>     
        <hr class="my-1">
        <div class="container-data">
          <div class="container-bars">
            <progressbar class="line" :label='"温度"' :unit='"℃"' :value=env.temperature :max=60></progressbar>
            <progressbar class="line" :label='"湿度"' :value=env.humidity></progressbar>
          </div>
          <div class="container-status">
            <div class="line-status line">
              <span class="badge badge-light label-line-status">时间</span>
              <span>{{env.time}}</span>
            </div>
            <div class="line-status line">
              <span class="badge badge-light label-line-status">主人状态</span>
              <span :class="[env.ownerstate ? 'badge-success':'badge-secondary','badge']">{{env.ownerstate?'在家':'不在家'}}</span>
            </div>     
          </div>
        </div>        
      </div>

      <!-- 设备列表 -->
      <div class="container-devices col-md-8">
        <h4 style="position:absolute;left:-3rem;top:-1rem;">你的设备</h4>
        <device v-for="device in devices" :key="device.id" :id="device.id" :type="device.type" 
        :state="device.state" :env="env" @deviceOperation='operate'></device>
      </div>
    </div>

    <!-- models -->
    <template>
      <!-- 添加设备modal -->
      <div class="modal" id="modal-device">
        <div class="modal-dialog">
          <div class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title">添加设备</h5>
              <button type="button" class="close" data-dismiss="modal">
                &times;
              </button>
            </div>
            <div class="modal-body">
              <div class="input-group mb-3">
                <div class="input-group-prepend">
                  <label class="input-group-text">设备类型</label>
                </div>
                <select class="custom-select" v-model="deviceidtoadd">
                  <option selected value="0">请选择要添加的设备类型</option>
                  <option value="1">One</option>
                  <option value="2">Two</option>
                  <option value="3">Three</option>
                </select>
              </div>
              <div class="container-device-detail" v-if="deviceidtoadd!=0">
                <div class="line-status line">
                  <span class="badge badge-light line-status-label">功率</span>
                  <span>40W</span>
                </div>
                <div class="line-status line">
                  <span class="badge badge-light line-status-label">电压</span>
                  <span>220V</span>
                </div>
              </div>
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-secondary" data-dismiss="modal">取消</button>
              <button type="button" class="btn btn-primary">确定</button>
            </div>
          </div>
        </div>
      </div>

      <!-- 日程管理modal -->
      <div class="modal" id="modal-schedule">
        <div class="modal-dialog">
          <div class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title">日程管理</h5>
              <button type="button" class="close" data-dismiss="modal">
                &times;
              </button>
            </div>
            <div class="modal-body">
              <table></table>
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-secondary" data-dismiss="modal">取消</button>
              <button type="button" class="btn btn-primary">确定</button>
            </div>
          </div>
        </div>
      </div>
    </template>
  </div>
</template>

<script>
import progressbar from './ProgressBar'
import device from './Device'
export default {
  name: "AppIndex",
  data() {
    return {
      environmentconsole: false,
      env:{
        temperature: 30,
        humidity: 4,
        time: '12:00',
        ownerstate:true
      },      
      voiceMessage: '',
      isVoiceAlert:false,
      voiceAlertMessage:'',
      deviceidtoadd:0,
      devices:[
        {
          id:1,
          name:'A1',
          type:'AirConditioner',
          state:1
        },
        {
          id:2,
          name:'H1',
          type:'Humidifier',
          state:1
        },
        {
          id:3,
          name:'L2',
          type:'Light',
          state:0
        },
        {
          id:4,
          name:'C2',
          type:'Curtain',
          state:0
        },
        {
          id:5,
          name:'B2',
          type:'Box',
          state:0
        },
        {
          id:6,
          name:'T2',
          type:'TV',
          state:0
        }
      ],
      isAlert:false,
      alertMessage:''
    }
  },
  methods:{
    /**
    发送语音消息
     */
    sendVoiceMessage(){
      console.log(this.voiceMessage)
    },
    /**
    操作设备(核心方法)
     */
    operate(data){
      console.log(data)
    }
  },components:{
    progressbar,
    device
  }
};
</script>

<style scoped>

  .container-main{
    padding-top: 1rem;
  }

  .navbar-right{
    margin-right: 10rem;
    display: flex;
    display: -webkit-box;
    display: -webkit-inline-box;
  }

  .console-environment{
    padding-top: 1rem;
    padding-bottom: 1rem;
    max-width: 40rem;
    margin: auto;
  }

  .console-environment-header{
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .btn-ownerstate{
    position: absolute;
    height: 1.5rem;
    right: 3rem;
    font-size: .7rem;
  }

  .container-data{
    display: flex;
  }

  .line{
    margin-top: .5rem;
  }

  .container-bars{
    padding-left: 1.5rem;
  }

  .container-status{
    margin-left: 4rem;
    flex-direction: column;
  }

  .line-status{
    align-items: center;
    width: fit-content;
    padding: .1rem 0;
    font-size: .9rem;
  }

  .label-line-status{
    margin-right: .75rem;
  }

  .badge-ownerstate{
    padding: .1rem;
  }

  /* devices */
  .container-devices{
    margin:auto;
    display: flex;
    flex-wrap: wrap;
    margin-top: 1rem;
    justify-content: space-around;
  }
</style>

