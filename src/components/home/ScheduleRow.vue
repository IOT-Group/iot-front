<template>
  <tr>
    <td>
      <button
        class="close"
        style="position:absolute;left:2rem"
        @click="$emit('deleteSchedule',{deviceId:schedule.deviceId,time:schedule.time,code:schedule.code})"
      >&times;</button>
      {{schedule.deviceId}}
    </td>
    <td>{{schedule.name}}</td>
    <td>{{schedule.code}}</td>
    <td>{{time}}</td>
  </tr>
</template>

<script>
export default {
  props: {
    schedule: {
      required: true
    }
  },computed:{
      time(){
        let t = this.schedule.time;
        let m = new String(this.schedule.time % 60);
        if (m.length == 1) m = m == "0" ? "00" : "0" + m;
        return Math.floor(t / 60) + ":" + m;      
      }
  }
};
</script>