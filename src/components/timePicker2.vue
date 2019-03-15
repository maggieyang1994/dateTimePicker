<template>
  <div class="timeWrapper">
    <div class="picker_title primary">
      <div class="picker-time-title">
        <div class="picker-time-title-time">
          <div
            class="v-picker__title__btn"
            @click="mode='hour'"
            :style="{opacity:mode==='hour'?1:0.7}"
          >{{hour}}</div>
          <span>:</span>
          <div
            class="v-picker__title__btn"
            @click="mode='minutes'"
            :style="{opacity:mode==='minutes'?1:0.7}"
          >{{String(minutes).length === 1 ? "0" + minutes: minutes}}</div>
        </div>
        <div class="picker-time-title-ampm">
          <div @click="isAm = true" :style="{opacity:isAm ?1:0.7}">AM</div>
          <div @click="isAm = false" :style="{opacity:!isAm?1:0.7}">PM</div>
        </div>
      </div>
    </div>
    <div class="picker-body">
      <div class="picker-container">
        <div
          class="time-picker-clock"
          @click="handlerClick($event)"
          @mousedown="draggeble = true"
          @mousemove="draggeble && handlerClick($event)"
          @mouseup="onMouseUp($event)"
        >
          <div class="time-picker-clock__hand primary" :style="{transform:`rotate(${deg}deg)`}"></div>
          <div>
            <div v-for="i in 12" :key="i" :style="getStyle(i)" class="v-time-picker-clock__item">
              <span
                :style="getSpanStyle(i)"
                class="hour"
              >{{mode === 'hour'? i : String((i*5 % 60)).padStart(2,0)}}</span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import moment from 'moment'
export default {
  props: {
    value: {
      type: String
    }
  },
  watch: {
    isAm(val) {
      if (val) this.time = this.hour + ":" + this.minutes;
      else this.time = (this.hour * 1 + 12 === 24 ? '00': this.hour * 1 + 12 ) + ":" + this.minutes;
    },
    hour(val) {
      this.time = (this.isAm ? val : (val * 1 + 12 === 24 ? '00': val * 1 + 12)) + ":" + this.minutes;
    },
    minutes(val) {
      this.time = this.hour + ":" + val;
    },
    time(val){
      this.$emit("input", moment("2012-12-12 " + val).format("HH:mm"))
    }
  },
  data() {
    return {
      mode: "hour",
      hour: this.value ? this.value.split(":")[0] : 12,
      minutes: this.value ? this.value.split(":")[1] : "00",
      draggeble: false,
      isAm: this.value ? (this.value.split(":")[0] > 12 ? false : true) : true,
      time: this.value
    };
  },
  computed: {
    deg() {
      return this.mode === "hour" ? this.hour * 30 : this.minutes * 6;
    }
  },
  methods: {
    getStyle(i) {
      return {
        transform: `rotate(${i * 30}deg)`
      };
    },
    getSpanStyle(i) {
      let styleObj = {};
      styleObj.transform = `rotate(${360 - i * 30}deg)`;
      if (
        (this.mode === "hour" && (i === this.hour * 1 || i === this.hour - 12)) ||
        (this.mode === "minutes" && (i * 5) % 60 === this.minutes * 1)
      ) {
        (styleObj.backgroundColor = "#1867c0"), (styleObj.color = "white");
      }
      return styleObj;
    },
    handlerClick(e) {
      // 获取鼠标点击位置
      let mouseClick = {
        x: e.clientX,
        y: e.clientY
      };
      // 获取圆心位置
      let dom = document.querySelector(".time-picker-clock");
      let center = {
        x: dom.getBoundingClientRect().left + parseInt(dom.offsetWidth / 2),
        y: dom.getBoundingClientRect().top + parseInt(dom.offsetHeight / 2)
      };
      let num = Math.round(this.angle(center, mouseClick) + 360) % 360;
      if (this.mode === "hour") {
        num = Math.round(num / 30);
        this.hour = num === 0 ? 12 : num;
      } else {
        num = Math.round(num / 6);
        this.minutes = num === 60 || num === 0 ? "00" : num;
      }
    },
    onMouseUp(e){
      this.draggeble = false;
      if(this.mode === 'minutes') {
        this.handlerClick(e);
        setTimeout(() => {
           this.$emit('closePicker')
        }, 0)
       
      }
    },
    angle(center, p1) {
      const value =
        2 *
        Math.atan2(
          p1.y - center.y - this.euclidean(center, p1),
          p1.x - center.x
        );
      return -(Math.abs((value * 180) / Math.PI) - 180);
    },
    euclidean(p0, p1) {
      const dx = p1.x - p0.x;
      const dy = p1.y - p0.y;
      return Math.sqrt(dx * dx + dy * dy);
    }
  }
};
</script>
<style scoped>
.v-time-picker-clock__item {
  width: 6px;
  height: 50%;
  /* background-color: black; */
  -webkit-transform-origin: 0 250px;
  transform-origin: 0 111px;
  position: absolute;
  left: calc(50%);
  top: 4px;
  z-index: 2;
}
/* panel */
.time-picker-clock__hand {
  height: calc(50% - 16px);
  width: 2px;
  bottom: 50%;
  left: calc(50% - 2px);
  -webkit-transform-origin: center bottom;
  transform-origin: center bottom;
  position: absolute;
  will-change: transform;
  z-index: 1;
}
.time-picker-clock__hand:before {
  background: transparent;
  border-width: 2px;
  border-style: solid;
  border-color: inherit;
  border-radius: 100%;
  width: 10px;
  height: 10px;
  content: "";
  position: absolute;
  top: -4px;
  left: 50%;
  -webkit-transform: translate(-50%, -50%);
  transform: translate(-50%, -50%);
}
.time-picker-clock__hand:after {
  content: "";
  position: absolute;
  height: 8px;
  width: 8px;
  top: 100%;
  left: 50%;
  border-radius: 100%;
  border-style: solid;
  border-color: inherit;
  background-color: inherit;
  -webkit-transform: translate(-50%, -50%);
  transform: translate(-50%, -50%);
}

.picker-body {
  /* border: 1px solid red; */
  padding-bottom: 17px;
  width: 100%;
  height: auto;
  overflow: hidden;
  position: relative;
  z-index: 0;
  flex: 1 0 auto;
  display: flex;
  flex-direction: column;
  align-items: center;
}
.picker-container {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 10px;
}
.time-picker-clock {
  background: #e0e0e0;
  border-radius: 50%;
  position: relative;
  transition: 0.3s cubic-bezier(0.25, 0.8, 0.5, 1);
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;

  width: 230px;
  height: 230px;
}
/* head */
.timeWrapper {
  /* top: -356px; */
  left: 281px;

  border-radius: 2px;
  contain: layout style;
  display: inline-flex;
  flex-direction: column;
  vertical-align: top;
  position: relative;
  min-width: 260px;
  border:1px solid rgba(0,0,0,.15)
  /* box-shadow: 0px 3px 1px -2px rgba(0,0,0,0.2), 0px 2px 2px 0px rgba(0,0,0,0.14), 0px 1px 5px 0px rgba(0,0,0,0.12); */
}
.picker_title {
  padding: 16px;
  color: #fff;
}
.picker-time-title {
  display: flex;
  line-height: 1;
  justify-content: center;
}
.primary {
  background-color: #1867c0;
  border-color: #1867c0;
}
.picker-time-title-time {
  white-space: nowrap;
}
.picker-time-title-ampm {
  align-self: flex-end;
  display: flex;
  flex-direction: column;
  font-size: 16px;
  margin: 8px 0 11px 8px;
  text-transform: uppercase;
  cursor: pointer;
}
.picker-time-title-time {
  align-items: center;
  display: inline-flex;
  height: 56px;
  font-size: 40px;
  justify-content: center;
}
.hour {
  width: 32px;
  height: 32px;
  line-height: 32px;
  text-align: center;
  display: inline-block;
  margin-left: -16px;
  border-radius: 50%;
}
.v-picker__title__btn{
  cursor: pointer
}
</style>


