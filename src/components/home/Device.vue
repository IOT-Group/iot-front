<template>
  <div class="container-device" style="width:fit-content">
    <div container-device-info>
      <img src />
      <div>id.{{id}}</div>
      <div>{{type}}</div>
    </div>
    <div class="container-device-operations" v-html="operations">
    </div>
    <a href="#" :class="['badge',state==0?'badge-success':'badge-danger']" @click="state=1-state">{{state==0?'打开':'关闭'}}</a>
  </div>
</template>

<script>
export default {
  name: 'Device',
  props: {
    id: {
      required: true
    },
    type: {
      default: "未知"
    },
    state: {
      default: 0
    },operations:{
        default: ""
    }
  }
};
</script>