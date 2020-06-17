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
          </ul>
          <ul class="navbar-nav">
            <li class="nav-item">
              <a href="#" class="nav-link">日程管理</a>
            </li>
          </ul>
        </div>
      </nav>
    </div>

    <!-- 主容器 -->
    <div class="container-fluid container-main">
      <!-- 环境控制台 -->
      <div class="collapse card console-environment" id="collapse-environment">
        <h4>环境控制台</h4>
        <hr class="my-3">
        <div class="container-bars">
          <progressbar :label='"温度"' :unit='"℃"' :value=temperature :max=60></progressbar>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import progressbar from './ProgressBar'
export default {
  name: "AppIndex",
  data() {
    return {
      environmentconsole: false,
      temperature: 30
    }
  },
  methods:{

  },components:{
    progressbar
  }
};
</script>

<style scoped>

  .container-main{
    padding-top: 1rem;
  }

  .console-environment{
    width: 40%;
  }
</style>

