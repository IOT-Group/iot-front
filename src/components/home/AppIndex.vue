<template>
  <div>
    <!-- 导航栏 -->
    <div class="container-navbar">
      <nav class="navbar navbar-expand-lg navbar-light bg-light">
        <span class="navbar-brand">{{username}}'s HOME</span>
        <button
          class="navbar-toggler"
          type="button"
          data-toggle="collapse"
          data-target="#navbar-content"
        >
          <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbar-content">
          <ul class="navbar-nav">
            <li class="nav-item" v-bind:class="{active:environmentconsole}">
              <a
                data-toggle="collapse"
                href="#collapse-environment"
                class="nav-link"
                v-on:click="environmentconsole=!environmentconsole"
              >环境管理</a>
            </li>
            <li class="nav-item">
              <a href="#" class="nav-link" data-toggle="modal" data-target="#modal-schedule">日程管理</a>
            </li>
            <li class="nav-item">
              <a href="#" class="nav-link" data-toggle="modal" data-target="#modal-device">添加设备</a>
            </li>
          </ul>
        </div>
        <div
          v-show="isAlert"
          :class="['alert',isErrorAlert?'alert-danger':'alert-success']"
          style="margin-bottom:0;padding:.3rem 1.25rem;margin-right:3rem"
        >{{alertMessage}}</div>
        <div
          v-show="isVoiceAlert"
          class="alert alert-success"
          style="margin-bottom:0;padding:.3rem 1.25rem;margin-right:3rem"
        >{{voiceAlertMessage}}</div>
        <div class="navbar-right">
          <div class="input-group">
            <div class="input-group-prepend">
              <span class="input-group-text">语音控制</span>
            </div>
            <input
              type="text"
              class="form-control"
              placeholder="请输入想要发送的语音信息"
              v-model="voiceMessage"
            />
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
          <button
            :class="['btn-sm btn btn-ownerstate',env.ownerstate?'btn-secondary':'btn-success',ownerstateChanging?'disabled':'']"
            @click="changeOwnerState"
          >
            <span class="spinner-grow spinner-grow-sm" v-show="ownerstateChanging"></span>
            <span>{{env.ownerstate?'出门':'回家'}}</span>
          </button>
        </div>
        <hr class="my-1" />
        <div class="container-data">
          <div class="container-bars">
            <progressbar
              class="line"
              :label="'温度'"
              :unit="'℃'"
              :value="env.temperature"
              :max="60"
              @setEnvironment="setEnvironment"
            ></progressbar>
            <progressbar
              class="line"
              :label="'湿度'"
              :value="env.humidity"
              @setEnvironment="setEnvironment"
            ></progressbar>
          </div>
          <div class="container-status">
            <div class="line-status line">
              <span class="badge badge-light label-line-status">时间</span>
              <span>{{timeStr}}</span>
              <div class="btn-group" style="margin-left:2rem">
                <button
                  :class="['btn btn-success btn-sm',timeStep==0?'disabled':'']"
                  id="btn-time-pause"
                  @click="timeStep=0"
                >暂停</button>
                <button
                  :class="['btn btn-success btn-sm',timeStep==5?'disabled':'']"
                  id="btn-time-5"
                  @click="timeStep=5"
                >5min</button>
                <button
                  :class="['btn btn-success btn-sm',timeStep==30?'disabled':'']"
                  id="btn-time-30"
                  @click="timeStep=30"
                >30min</button>
              </div>
            </div>
            <div class="line-status line">
              <span class="badge badge-light label-line-status">主人状态</span>
              <span
                :class="[env.ownerstate ? 'badge-success':'badge-secondary','badge']"
              >{{env.ownerstate?'在家':'不在家'}}</span>
            </div>
          </div>
        </div>
      </div>

      <!-- 设备列表 -->
      <div class="container-devices col-md-8">
        <h4 style="position:absolute;left:-3rem;top:-1rem;">
          你的设备
          <span v-if="isDeviceLoading" style="margin-left:2rem;">
            <span class="spinner-grow spinner-grow-lg"></span>加载中...
          </span>
        </h4>
        <device
          v-for="device in devices"
          :key="device.id"
          :id="device.deviceId"
          :type="device.deviceType"
          :state="device.state"
          :env="env"
          @deviceOperation="operate"
          @deleteDevice="deleteDevice"
        ></device>
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
              <button type="button" class="close" data-dismiss="modal">&times;</button>
            </div>
            <div class="modal-body">
              <div class="input-group mb-3">
                <div class="input-group-prepend">
                  <label class="input-group-text">设备类型</label>
                </div>
                <select class="custom-select" v-model="deviceTypeIdToAdd">
                  <option selected value="0">请选择要添加的设备类型</option>
                  <option v-for="type in deviceTypes" :key="type.id" :value="type.id">{{type.name}}</option>
                </select>
              </div>
              <div class="container-device-detail" v-if="deviceTypeIdToAdd!=0">
                <div class="line-status line">
                  <span class="badge badge-light line-status-label">功率</span>
                  <span>{{deviceTypeToAdd.power}}W</span>
                </div>
                <div class="line-status line">
                  <span class="badge badge-light line-status-label">电压</span>
                  <span>{{deviceTypeToAdd.voltage}}V</span>
                </div>
              </div>
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-secondary" data-dismiss="modal">取消</button>
              <button type="button" class="btn btn-primary" @click="addDevice">确定</button>
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
              <div
                class="alert alert-success"
                v-show="isScheduleAlert"
                style="margin-bottom:0;padding:.3rem 1.25rem;margin:auto"
              >{{scheduleAlertMessage}}</div>
              <button type="button" class="close" data-dismiss="modal">&times;</button>
            </div>
            <div class="modal-body">
              <table class="table table-striped">
                <thead>
                  <tr>
                    <th>设备编号</th>
                    <th>设备名</th>
                    <th>操作</th>
                    <th>时间</th>
                  </tr>
                </thead>
                <tbody>
                  <schedule-row
                    v-for="item in sortedSchedule"
                    :key="item.time"
                    :schedule="item"
                    @deleteSchedule="deleteSchedule"
                  ></schedule-row>
                  <tr>
                    <th>
                      <button
                        class="close"
                        style="position:absolute;left:2rem"
                        @click="addSchedule"
                      >&plus;</button>
                      <input
                        type="text"
                        class="form-control input-schedule"
                        style="margin-left:2.5rem"
                        v-model="scheduleToAdd.deviceId"
                      />
                    </th>
                    <th>{{scheduleAddName}}</th>
                    <th>
                      <input
                        type="text"
                        class="form-control input-schedule"
                        v-model="scheduleToAdd.code"
                      />
                    </th>
                    <th>
                      <input
                        type="text"
                        class="form-control input-schedule"
                        v-model="scheduleToAdd.time"
                      />
                    </th>
                  </tr>
                </tbody>
              </table>
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-secondary" data-dismiss="modal">退出</button>
            </div>
          </div>
        </div>
      </div>

      <!-- 人脸识别modal -->
      <div class="modal" id="modal-face">
        <div class="modal-dialog">
          <div class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title">人脸识别中</h5>
              <button type="button" class="close" data-dismiss="modal">&times;</button>
            </div>
            <div class="modal-body">
              <div class="spinner-grow"></div>
            </div>
          </div>
        </div>
      </div>
    </template>
  </div>
</template>

<script>
import progressbar from "./ProgressBar";
import device from "./Device";
import scheduleRow from "./ScheduleRow";
export default {
  name: "AppIndex",
  data() {
    return {
      username: this.$route.params.username,
      environmentconsole: false,
      ownerstateChanging: false,
      timeStep: 0,
      env: {},
      voiceMessage: "",
      isVoiceAlert: false,
      voiceAlertMessage: "",
      devices: [],
      isDeviceLoading: false,
      isAlert: false,
      isErrorAlert: false,
      alertMessage: "",
      schedule: [],
      scheduleToAdd: {
        deviceId: "",
        time: 0,
        code: "0"
      },
      isScheduleAlert: false,
      scheduleAlertMessage: "",
      deviceTypeIdToAdd: 0,
      deviceTypes: [
        {
          id: 1,
          name: "A1",
          type: "Airconditioner",
          power: 1000,
          voltage: 220
        },
        {
          id: 2,
          name: "A2",
          type: "Airconditioner",
          power: 1500,
          voltage: 220
        },
        {
          id: 3,
          name: "L1",
          type: "Light",
          power: 20,
          voltage: 220
        },
        {
          id: 4,
          name: "L2",
          type: "Light",
          power: 50,
          voltage: 220
        },
        {
          id: 5,
          name: "L3",
          type: "Light",
          power: 75,
          voltage: 220
        },
        {
          id: 6,
          name: "T1",
          type: "TV",
          power: 100,
          voltage: 220
        },
        {
          id: 7,
          name: "T2",
          type: "TV",
          power: 250,
          voltage: 220
        },
        {
          id: 8,
          name: "C1",
          type: "Curtain",
          power: 50,
          voltage: 220
        },
        {
          id: 9,
          name: "C2",
          type: "Curtain",
          power: 60,
          voltage: 220
        },
        {
          id: 10,
          name: "C3",
          type: "Curtain",
          power: 70,
          voltage: 220
        },
        {
          id: 11,
          name: "H1",
          type: "Humidifier",
          power: 15,
          voltage: 220
        },
        {
          id: 12,
          name: "H2",
          type: "Humidifier",
          power: 20,
          voltage: 220
        },
        {
          id: 13,
          name: "B1",
          type: "Box",
          power: 10,
          voltage: 220
        },
        {
          id: 14,
          name: "B2",
          type: "Box",
          power: 20,
          voltage: 220
        }
      ]
    };
  },
  computed: {
    timeStr: function() {
      let minute = new String(this.env.time % 60);
      if (minute.length == 1) minute = minute == "0" ? "00" : "0" + minute;
      return Math.floor(this.env.time / 60) + ":" + minute;
    },
    sortedSchedule: function() {
      let [...list] = this.schedule;
      for (let i = 0; i < list.length; i++) {
        for (let j = 0; j < this.devices.length; j++) {
          if (list[i].deviceId == this.devices[j].deviceId) {
            list[i].name = this.devices[j].deviceType;
            break;
          }
        }
      }
      list.sort((x, y) => {
        return x.time - y.time;
      });
      return list;
    },
    scheduleAddName() {
      for (let i = 0; i < this.devices.length; i++) {
        if (this.devices[i].deviceId == this.scheduleToAdd.deviceId) {
          return this.devices[i].deviceType;
        }
      }
      return "";
    },
    deviceTypeToAdd() {
      for (let i = 0; i < this.deviceTypes.length; i++) {
        if (this.deviceTypes[i].id == this.deviceTypeIdToAdd)
          return this.deviceTypes[i];
      }
      return { id: 0 };
    }
  },
  methods: {
    /**
    1. 发送语音消息
     */
    sendVoiceMessage() {
      this.$axios
        .post("/voice", {
          voiceInput: this.voiceMessage,
          username: this.username,
          time:this.env.time
        })
        .then(res => {
          console.log(res)
          if (res.data.succ) {
            for(let i=0;i<this.devices.length;i++){
              if(this.devices[i].deviceId==res.data.content.id){
                this.devices[i].state=res.data.content.state
                break
              }
            }
            alertMsg('语音操作成功!')
          } else {
            console.error(res);
            alertMsg("发送语音失败", true);
          }
        });
    },
    /**
    2. 操作设备
     */
    operate(data) {
      console.log(data);
      this.$axios
        .post("/operateDevice", {
          deviceId: data.id,
          code: data.code,
          time: this.env.time,
          timeInterval: this.timeStep
        })
        .then(res => {
          console.log(res);
          if (res.data.succ) {
            this.alertMsg("操作成功!");
            for (let i = 0; i < this.devices.length; i++) {
              if (this.devices[i].deviceId == data.id) {
                this.devices[i].state = data.code;
                console.log(this.devices[i])
              }
            }
          } else {
            this.alertMsg("操作失败!", true);
          }
        })
    },
    /**
     * 3. 更改主人状态
     */
    changeOwnerState() {
      this.ownerstateChanging = true;
      this.$axios
        .post("/rec", {
          username: this.username,
          state: this.env.ownerstate ? "0" : "1",
          time: this.env.time,
          timeInterval: this.timeStep
        })
        .then(res => {
          console.log(res);
          if (res.data.succ) {
            
            console.log('yes')
            if (this.env.ownerstate == false) {
              $("#modal-face").modal("show");
              setTimeout(() => {
                this.devices=res.data.content.devices
                $("#modal-face").modal("hide");
                this.env.ownerstate = !this.env.ownerstate;
                this.ownerstateChanging = false;
                this.alertMsg("认证成功!");
              }, 2500);
            } else {
              setTimeout(() => {
                this.devices=res.data.content.devices
                this.env.ownerstate = !this.env.ownerstate;
                this.ownerstateChanging = false;
                this.alertMsg("主人已离开.");
              }, 2000);
            }
          } else {
            this.alertMsg("认证失败!");
            this.ownerstateChanging = false;
          }
        })
        .catch(err => {
          console.log(err);
          this.alertMsg("网络错误!", true);
        });
    },
    /**
     * 操作成功后的消息提示
     */
    alertMsg(msg, isErrorAlert = false, time = 2500) {
      this.isErrorAlert = isErrorAlert;
      this.isAlert = true;
      this.alertMessage = msg;
      setTimeout(() => {
        this.isAlert = false;
      }, time);
    },
    /**
     * 4. 删除设备
     */
    deleteDevice(id) {
      id = id.id;
      let flag = confirm("确认删除该设备吗？");
      if (flag) {
        this.$axios
          .post("/deleteDevice", {
            id: id
          })
          .then(res => {
            console.log(res);
            if (res.status == "200") {
              for (let i = 0; i < this.devices.length; i++) {
                if (this.devices[i].deviceId == id) {
                  this.devices.splice(i, 1);
                  this.alertMsg("删除设备成功!");
                  break;
                }
              }
            } else {
              console.error(res);
              alertMsg("删除失败!", true);
            }
          });
      }
    },
    /**
     * 5. 删除日程
     */
    deleteSchedule(schedule) {
      console.log(schedule);

      this.$axios.post("/deleteCalendar", schedule).then(res => {
        if (res.data.succ) {
          for (let i = 0; i < this.schedule.length; i++) {
            if (
              this.schedule[i].deviceId == schedule.deviceId &&
              this.schedule[i].time == schedule.time &&
              this.schedule[i].code == schedule.code
            ) {
              this.isScheduleAlert = true;
              this.scheduleAlertMessage = "删除成功!";
              setTimeout(() => {
                this.isScheduleAlert = false;
              }, 1200);
              this.schedule.splice(i, 1);
              break;
            }
          }
        } else {
          console.error(res);
        }
      });
    },
    /**
     * 6. 添加日程
     */
    addSchedule() {
      this.scheduleToAdd.name = this.scheduleAddName;
      var schedule = JSON.parse(JSON.stringify(this.scheduleToAdd));
      console.log(schedule);
      this.$axios.post("/addCalendar", schedule).then(res => {
        if (res.data.succ) {
          this.schedule.push(schedule);
        }
      });
    },
    /**
     * 7. 获取用户设备
     */
    getDevices() {
      this.isDeviceLoading = true;
      this.$axios
        .post("/getDevices", {
          username: this.username
        })
        .then(res => {
          this.isDeviceLoading = false;
          if (res.data.succ) {
            this.devices = res.data.content;
          }
        });
    },
    /**
     * 8. 获取日程表
     */
    getSchedule() {
      this.$axios
        .post("/showCalendar", {
          username: this.username
        })
        .then(res => {
          if (res.data.succ) {
            this.schedule = res.data.content;
          } else {
            console.error(res);
          }
        });
    },
    /**
     * 9. 获取支持的设备类型
     */
    getDeviceTypes() {
      this.$axios.post("/getDeviceTypes", {}).then(res => {
        if (res.succ) {
          this.deviceTypes = res.content;
        } else {
          console.error(res);
        }
      });
    },
    /**
     * 10. 添加设备
     */
    addDevice() {
      let typeToAdd = "";
      let types = [
        "A1",
        "A2",
        "L1",
        "L2",
        "L3",
        "T1",
        "T2",
        "C1",
        "C2",
        "C3",
        "H1",
        "H2",
        "B1",
        "B2"
      ];
      typeToAdd = types[this.deviceTypeIdToAdd-1];
      // var a = {p:0}
      // var x=a.p
      // for(x=0;x<14;x++){
      //   if(this.devicetypes[x].id==this.deviceTypeIdToAdd){
      //     typeToAdd=this.devicetypes[x].name
      //   }
      // }
      console.log(typeToAdd);
      var result = this.$axios
        .post("/addDevice", {
          username: this.username,
          type: typeToAdd
        })
        .then(res => {
          if (res.status == "200") {
            console.log(res.data);
            res.data.deviceType = res.data.typeid;
            res.data.deviceId = res.data.id;
            this.devices.push(res.data);
            $("#modal-device").modal("hide");
            this.alertMsg("添加设备成功");
          }
        });

      // .then(res => {
      //   console.log(res)
      //   $("#modal-device").modal("hide");
      //   this.devices.push(res.data.content);
      //   this.alertMsg("添加设备成功!");
      // });
    },
    /**
     * 11. 定时发送环境
     */
    sendEnvironment() {
      // console.log("sendingEnvironment");
      // console.log(this.env.time)
      if (this.timeStep == 0) {
        setTimeout(() => {
          this.sendEnvironment();
        }, 2000);
      } else {
        console.log('time:'+this.env.time)
        this.$axios
          .post("/sendEnvironment", {
            username: this.username,
            time: this.env.time,
            temperature: this.env.temperature,
            humidity: this.env.humidity,
            ownerState: this.env.ownerstate ? "1" : "0",
            timeInterval: this.timeStep
          })
          .then(res => {
            console.log(res.data);
            if (res.data.succ) {
              this.env = res.data.content.environment;
              this.devices = res.data.content.devices;
              this.env.ownerstate = res.data.content.environment.ownerState;
              this.env.time = (Number(this.env.time) + Number(this.timeStep)) % 1440;
            }
            setTimeout(() => {
              this.sendEnvironment();
            }, 2000);
          })
          .catch(err => {
            setTimeout(() => {
              this.sendEnvironment();
            }, 10000);
          });
      }
    },
    /**
     * 12. 获取用户环境
     */
    getEnvironment() {
      this.$axios
        .post("Environment/get", { username: this.username })
        .then(res => {
          console.log(res);
          if (res.data.succ) {
            this.env = res.data.content;
          }
        });
    },
    /**
     * 13. 设置用户环境
     */
    setEnvironment(data) {
      console.log(data);
      this.$axios
        .post("/Environment/change", {
          username: this.username,
          type: data.type,
          ins: data.ins
        })
        .then(res => {
          console.log(res);
          this.env = res.data.content;
          this.env.ownerstate = res.data.ownerState;
          if (res.data.succ) {
            if (data.type == 0) this.alertMsg("修改环境温度成功!");
            else if (data.type == 1) this.alertMsg("修改环境湿度成功!");
          }
        });
    }
  },
  created() {
    this.getDevices();
    this.getSchedule();
    this.getEnvironment();

    setTimeout(() => {
      this.sendEnvironment();
    }, 2000);
  },
  components: {
    progressbar,
    device,
    scheduleRow
  }
};
</script>

<style scoped>
.container-main {
  padding-top: 1rem;
}

.navbar-right {
  margin-right: 10rem;
  display: flex;
  display: -webkit-box;
  display: -webkit-inline-box;
}

.console-environment {
  padding-top: 1rem;
  padding-bottom: 1rem;
  max-width: 50rem;
  margin: auto;
}

.console-environment-header {
  display: flex;
  justify-content: center;
  align-items: center;
}

.console-environment .btn-sm {
  max-height: 1.6rem;
  font-size: 0.8rem;
}

.btn-ownerstate {
  position: absolute;
  height: 1.5rem;
  right: 3rem;
  font-size: 0.7rem;
}

.container-data {
  display: flex;
}

.line {
  margin-top: 0.5rem;
}

.container-bars {
  padding-left: 1.5rem;
}

.container-status {
  margin-left: 2rem;
  flex-direction: column;
}

.line-status {
  align-items: center;
  width: fit-content;
  padding: 0.1rem 0;
  font-size: 0.9rem;
  padding-top: 1rem;
}

.label-line-status {
  margin-right: 0.75rem;
}

.badge-ownerstate {
  padding: 0.1rem;
}

.input-schedule {
  max-width: 5rem;
}

/* devices */
.container-devices {
  margin: auto;
  display: flex;
  flex-wrap: wrap;
  margin-top: 1rem;
  justify-content: space-around;
}
</style>

