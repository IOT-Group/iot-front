<template>
  <div class="container-device card" style="width:fit-content;padding:1rem;margin:1.5rem">
    <div container-device-info style="display:flex;margin-bottom:1.5rem">
      <img :src="'/static/'+type+'.png'" class="img-device">
      <div style="margin-left:1rem">
        <div><strong>ID.{{id}}</strong></div>
        <div>{{type}}</div>
        <indicator :state="state" style="margin:auto"></indicator>
      </div>     
    </div>
    <div class="container-device-operations">
      <div v-if="type=='AirConditioner'"></div>
      <div v-if="type=='AirConditioner' || type=='Light' || type=='TV' || type=='Curtain' || type=='Humidifier' || type=='Box'">
        <a href="#" :class="['badge',state==0?'badge-success':'badge-danger']" @click="state=1-state">{{state==0?'打开':'关闭'}}</a>
      </div>      
    </div> 
  </div>
</template>

<script>
import indicator from "./Indicator";
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
    }
  },components:{
    indicator
  }
};
</script>

<style scoped>
  .img-device{
    width: 6rem;
    height: 6rem;
  }
</style>