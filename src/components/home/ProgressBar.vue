<template>
    <div class="container">
        <span class="badge badge-light">{{label}}</span>
        <div class="bar-outter" :style="{backgroundColor:backgroundcolor,width:'10rem',height:'.25rem'}">
            <div class="bar-inner" :style="{backgroundColor:color,width:(value/max*10)+'rem',height:'.25rem'}"></div>
        </div>
        <div class="detail">{{value}}{{unit}}</div>       
    </div>    
</template>

<script>
export default {
  name: "ProgressBar",
  props:{
      color:{
          default:'#28a745'
      },
      backgroundcolor:{
          default:'#f8f9fa'
      },max:{
          default:100
      },value:{
          default:0
      },label:{
          default:'label'
      },unit:{
          default: ''
      }
  }
};
</script>

<style scoped>
    .container{
        display: flex;
        align-items: center;
        width: fit-content;
        padding-top: .25rem;
        padding-bottom: .25rem;
    }
    .bar-outter{
        margin-left: 0.5rem;
    }
    .detail{
        font-size: 8px;
        margin-left: .75rem;
    }
</style>