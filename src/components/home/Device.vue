<template>
  <div class="container-device card" style="width:fit-content;padding:2rem;margin:1.5rem">
    <button
      class="close"
      style="position:absolute;right:.8rem;top:.8rem;"
      @click="$emit('deleteDevice',{id:id})"
    >&times;</button>
    <div container-device-info style="display:flex;margin-bottom:1.5rem">
      <img :src="'/static/'+type[0]+'.png'" class="img-device" />
      <div style="margin-left:1rem">
        <div>
          <strong>ID.{{id}}</strong>
        </div>
        <div>{{type}}</div>
        <indicator :state="indicatorState" style="margin:auto"></indicator>
      </div>
    </div>
    <div class="container-device-operations" style="display:flex;justify-content:space-around">
      <div v-if="type[0]=='A'" style="display:flex">
        <div class="input-group" style="max-width:6rem;max-height:1.8rem;">
          <input
            type="text"
            class="form-control"
            style="max-height:inherit;padding:.2rem;"
            v-model="input"
          />
          <div class="input-group-append">
            <span
              class="input-group-text"
              style="max-height:inherit;padding:.2rem;font-size:.6rem;"
            >℃</span>
          </div>
        </div>
        <button
          class="btn btn-success"
          style="margin-left:.4rem;margin-right:1rem;max-height:1.8rem;font-size:12px"
          @click="$emit('deviceOperation',{id:id,code:String(stateD)})"
        >设置</button>
      </div>
      <div
        v-if="type[0]=='A' || type[0]=='L' || type[0]=='T' || type[0]=='C' || type[0]=='H' || type[0]=='B'"
      >
        <a
          href="#"
          :class="['badge',indicatorState==0?'badge-success':'badge-danger']"
          @click="$emit('deviceOperation',{id:id,code:btnCode});"
        >{{indicatorState==0?'打开':'关闭'}}</a>
      </div>
    </div>
  </div>
</template>

<script>
import indicator from "./Indicator";
export default {
  name: "Device",
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
    env: {
      required: true
    }
  },
  data() {
    return {
      envD: this.env,
      stateD: this.state,
      input: this.state,
      indicatorState:this.state
    };
  },
  computed: {
    btnCode: function() {
      if (this.type[0] == "A") {
        return this.state == 0 ? this.input : "0";
      } else {
        return this.state == 0 ? "1" : "0";
      }
    }
  },
  watch:{
    state:function(){
      this.input = this.state
      this.indicatorState=this.state
    },
    input:function(){
      this.stateD=this.input
    }
  },
  components: {
    indicator
  },
  methods: {}
};
</script>

<style scoped>
.img-device {
  width: 6rem;
  height: 6rem;
}
</style>