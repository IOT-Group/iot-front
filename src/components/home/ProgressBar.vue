<template>
  <div class="container-progressbar">
    <span class="badge badge-light">{{label}}</span>
    <div
      class="bar-outter"
      :style="{backgroundColor:backgroundcolor,width:'10rem',height:'.45rem'}"
    >
      <div
        class="bar-inner"
        :style="{backgroundColor:color,width:((value-min)/(max-min)*10)+'rem',height:'.45rem'}"
      ></div>
    </div>
    <div style="display:-webkit-box;position:relative;top:.5rem;">
      <div class="detail">
        <input
          class="form-control"
          type="text"
          v-model="value"
          style="max-width:4rem;max-height:1.8rem;font-size:12px;"
        />
        {{unit}}
      </div>
      <div
        class="btn btn-success"
        style="margin-left:1rem;max-width:6rem;max-height:1.8rem;font-size:12px;"
        @click="$emit('setEnvironment',{
      type:setType,
      ins:value
    })"
      >修改</div>
    </div>
  </div>
</template>

<script>
export default {
  name: "ProgressBar",
  props: {
    backgroundcolor: {
      default: "#f8f9fa"
    },
    max: {
      default: 100
    },
    min: {
      default: 0
    },
    value: {
      default: 0
    },
    label: {
      default: "label"
    },
    unit: {
      default: ""
    }
  },
  computed: {
    color: function() {
      let rv = this.value - this.min;
      let rm = this.max - this.min;
      if (rv / rm < 0.25) {
        return "#007bff";
      } else if (rv / rm < 0.55) {
        return "#28a745";
      } else if (rv / rm < 0.85) {
        return "#ffc107";
      } else {
        return "#dc3545";
      }
    },
    setType: function() {
      if (this.label == "温度") return "0";
      else if (this.label == "湿度") return "1";
      return "";
    }
  }
};
</script>

<style scoped>
.container-progressbar {
  display: flex;
  align-items: center;
  width: fit-content;
  padding-top: 0.25rem;
  padding-bottom: 0.25rem;
}
.bar-outter {
  margin-left: 0.5rem;
}
.detail {
  font-size: 12px;
  margin-left: 0.75rem;
}
</style>