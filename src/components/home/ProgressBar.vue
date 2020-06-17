<template>
  <div class="container-progressbar">
    <span class="badge badge-light">{{label}}</span>
    <div
      class="bar-outter"
      :style="{backgroundColor:backgroundcolor,width:'10rem',height:'.25rem'}"
    >
      <div
        class="bar-inner"
        :style="{backgroundColor:color,width:((value-min)/(max-min)*10)+'rem',height:'.25rem'}"
      ></div>
    </div>
    <div class="detail">{{value}}{{unit}}</div>
  </div>
</template>

<script>
export default {
  name: "ProgressBar",
  props: {
    color: {
      default: "#28a745"
    },
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
  font-size: 8px;
  margin-left: 0.75rem;
}
</style>