<template>
  <div class="container-device card" style="width:fit-content;padding:2rem;margin:1.5rem">
    <div container-device-info style="display:flex;margin-bottom:1.5rem">
      <img :src="'/static/'+type+'.png'" class="img-device">
      <div style="margin-left:1rem">
        <div><strong>ID.{{id}}</strong></div>
        <div>{{type}}</div>
        <indicator :state="stateD" style="margin:auto"></indicator>
      </div>     
    </div>
    <div class="container-device-operations" style="display:flex;justify-content:space-around">
      <div v-if="type=='AirConditioner'" style="display:flex">
        <div class="input-group" style="max-width:6rem;max-height:1.8rem;">
          <input type="text" class="form-control" style="max-height:inherit;padding:.2rem;" v-model="temperatureD">
          <div class="input-group-append">
            <span class="input-group-text" style="max-height:inherit;padding:.2rem;font-size:.6rem;">℃</span>
          </div>
        </div>       
        <button class="btn btn-success" style="margin-left:.4rem;margin-right:1rem;max-height:1.8rem;font-size:12px" 
        @click="$emit('deviceOperation',{id:id,code:'A_SetTo_'+temperature})">设置</button>
      </div>
      <div v-if="type=='AirConditioner' || type=='Light' || type=='TV' || type=='Curtain' || type=='Humidifier' || type=='Box'">
        <a href="#" :class="['badge',stateD==0?'badge-success':'badge-danger']"
         @click="$emit('deviceOperation',{id:id,code:btnCode});stateD=1-stateD">{{stateD==0?'打开':'关闭'}}{{btnCode}}</a>
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
    },
    temperature:{
      default: 30
    },
    env:{
      required:true
    }
  },
  data(){
    return{
      stateD:this.state,
      temperatureD:this.temperature,
      envD:this.env
    }
  },
  computed:{
    btnCode: function(){
      if(this.type=='AirConditioner'){
        return this.stateD==0?'A_PowerOn_'+this.temperatureD:'A_PowerOff'
      }else{
        return this.type[0]+'Power'+(this.stateD==0?'On':'Off')
      }
    }
  },
  components:{
    indicator
  },
  methods:{
  }
};
</script>

<style scoped>
  .img-device{
    width: 6rem;
    height: 6rem;
  }
</style>