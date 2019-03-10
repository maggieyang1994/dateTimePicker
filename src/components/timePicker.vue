<template>
  <div>
    

    <!-- <div class='input-group date'>
        <input type='text' 
           v-model="time"/>
        <span class="input-group-addon" style="display:block" @click.prevent="focusHandler">
            <span class="fa fa-calendar"></span>
        </span>
    </div> -->


    
      <div 
        class="timepicker">
          <div class="clock-wrap">
            <div class="clock">

              <div class="timedisplay">
                <div class="innerdisplay">
                  <div class="time-left"></div>
                  <div class="showtime">
                    <span 
                      class="hour" 
                      @click="mode = 'hour'" 
                      :style="{ opacity: mode === 'hour' ? 1 : 0.7 }">{{fixHour(hour)}}</span>
                    <span>:</span>
                    <span 
                      class="minutes" 
                      @click="mode = 'minutes'"
                      :style="{ opacity: mode === 'minutes' ? 1 : 0.7 }">{{fixMinutes(minutes)}}</span>
                      <span class="am ampm" @click = 'isAm = true' :style="{ opacity: isAm === true ? 1 : 0.7 }">AM</span>
                      <span class="pm ampm" @click = "isAm = false" :style="{ opacity: isAm === false ? 1 : 0.7 }">PM</span>
                  </div>
                  <div class="time-right"></div>
                </div>
              </div>
              
              <div class="clockpanel">
                <div class="clockbg"></div>
                <div class="hourpicker">
                  <div 
                    class="selector"
                    :style="selectorRotateAngle"></div>
                  <span 
                    class="hourtxt" 
                    v-for="i in 12"
                    v-bind:key ="i"
                    :style="getHourStyle(i % 12)"
                    @click ="hanlderClick(i)"
                   >{{mode === 'hour' ? i : (5 * i) % 60 || '00'}}</span>
                   
                </div>
              </div>

             
            </div>
          </div>
      </div>
   
 
  </div>
</template>

<script>
import '../styles/css/bootstrap.min.css'
import '../styles/css/bootstrap-glyphicons.css'
import moment from "moment";

// import { idMixin, formMixin, formSizeMixin, formStateMixin } from '../mixins'
// import ComponentMixin from '@/plugins/componentMixin'
// import store from '@/store'

const positions = [
  [0, 5/1.3-10],
  [54.5/1.3, 16.6/1.3-10],
  [94.4/1.3, 59.5/1.3-10],
  [109/1.3, 114/1.3-10],
  [94.4/1.3, 168.5/1.3-10],
  [54.5/1.3, 208.4/1.3-10],
  [0, 223/1.3-10],
  [-54.5/1.3, 208.4/1.3-10],
  [-94.4/1.3, 168.5/1.3-10],
  [-109/1.3, 114/1.3-10],
  [-94.4/1.3, 59.5/1.3-10],
  [-54.5/1.3, 19.6/1.3-10],
]

export default {
  name: 'rais-clockwiseTimePicker',
  // mixins: [idMixin, formMixin, formSizeMixin, formStateMixin, ComponentMixin],
  props: {
    value: {
      type: String
    }
  },
  data () {
    return {
      mode: 'hour',
      time: this.value,
      hour: this.value ? moment("2012-12-12 " +this.value).format("HH"): 12,
      minutes: this.value ? moment("2012-12-12 " + this.value).format("mm") : 0,
      isAm: this.value.split(":")[0] <= 12
    }
  },
  watch: {
    time(newV, oldV){
      if(newV !== oldV){
        this.$emit("input", newV)
      }
    },
    minutes(value){
      this.time = moment("2012-12-12 " + this.hour +":" + value).format("HH:mm")
    },
    hour(value){
      this.time = moment("2012-12-12 " + (value % 12 === 0 ? (this.isAm? 0 : 12):value) +":" + this.minutes).format("HH:mm")
    },
    isAm(value){
      if(value){
        this.hour = this.hour - 12
      }else{
        this.hour = this.hour + 12 
      }
     
    }
  },
  computed: {
    selectorRotateAngle () {
      if (this.mode === 'hour') {
        return {
          transform: `rotateZ(${this.hour * 30 + 'deg'})`
        }
      } else {
        return {
          transform: `rotateZ(${this.minutes * 6 + 'deg'})`
        }
      }
    },
    inputClass () {
      return 'form-control'
    },
  },
  methods: {
    hanlderClick(i){
       this.mode === 'hour' ? (this.hour =  this.isAm ? i : i + 12): this.minutes = 5 * i % 60
    },
    getHourStyle (i) {
      const hasSelected = (this.mode === 'hour' &&  this.hour % 12 === i) 
        || (this.mode === 'minutes' && this.minutes === i * 5)
      const styleObj = {
        transform: `translate(${positions[i][0] + 'px'}, ${positions[i][1] + 'px'})`,
        background: hasSelected ? '#1867c0' : 'rgba(255, 255, 255, 0)',
        color: !hasSelected ? '#2c3e50' : '#FFF'
      }
      return styleObj
    },

    fixHour (h) {
      h = h >= 12? (h-12) *1: h * 1
      return (h + 11) % 12 + 1
    },

    fixMinutes (m) {
      return m % 60 < 10 ? '0' + m % 60 : m % 60
    }
  }
}
</script>

<style scoped>

  * {
    font-family: Roboto;
  }

  input {
    outline: none;
  }

.clock-wrap {
    /* margin-left:282px; */
    width: 259px;
    border-radius: 2px;
    background-color: rgb(255, 255, 255)
  }
 

  .clock {
    box-shadow: rgba(0, 0, 0, -0.752941) 0px 7px 45px, rgba(0, 0, 0, 0.219608) 0px 10px 18px;
    border-radius: 2px;
  }

  .timedisplay {
    padding: 29px 0;
    border-top-left-radius: 2px;
    border-top-right-radius: 2px;
    background-color: #1867c0;
    color: white;
  }

  .innerdisplay {
    margin: 6px 0px;
    line-height: 20px;
    height: 20px;
    font-size: 38px;
    display: flex;
    justify-content: center;
    align-items: baseline;
    position:relative
  }

  .time-left, .time-right {
    flex: 1 1 0%;
    position: relative;
    line-height: 17px;
    height: 17px;
    font-size: 17px;
  }

  .showtime {
    margin: 0px 10px;
  }

  .hour, .minutes {
    cursor: pointer;
  }

  .clockpanel {
    height: 246px;
    padding: 10px;
    position: relative;
    box-sizing: content-box;
  }

 
  .clockbg {
    position: absolute;
    top: 10px;
    left:32px;
    width: 200px;
    height: 200px;
    border-radius: 100%;
    background-color: rgba(0, 0, 0, 0.0666667);
  }

  .hourpicker {
    height: 100%;
    width: 100%;
    border-radius: 100%;
    position: relative;
    /*pointer-events: none;*/
    box-sizing: border-box;
  }

  .hourtxt {
    display: inline-block;
    position: absolute;
    width: 32px;
    height: 32px;
    border-radius: 50%;
    left: calc(50% - 16px);
    top: 8px;
    text-align: center;
    padding-top: 5px;
    font-size: 1.1em;
    cursor: pointer;
    /*pointer-events: none;*/
  }

  .selector {
    height: 30%;
    background: #1867c0;
    width: 2px;
    left: calc(50% - 1px);
    position: absolute;
    bottom: 60%;
    transform-origin: center bottom 0px;
    pointer-events: none;
  }


  .actionbuttons {
    padding: 8px;
    width: 100%;
    text-align: right;
    margin-top: 0px;
    border-top: none;
  }

  .actionbuttons button {
    border: 10px;
    box-sizing: border-box;
    display: inline-block;
    -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
    cursor: pointer;
    text-decoration: none;
    margin: 0px;
    padding: 0px;
    outline: none;
    font-size: inherit;
    font-weight: inherit;
    transform: translate(0px, 0px);
    height: 20px;
    line-height: 20px;
    color: #1867c0;
    transition: all 450ms cubic-bezier(0.23, 1, 0.32, 1) 0ms;
    border-radius: 2px;
    position: relative;
    overflow: hidden;
    background-color: rgba(0, 0, 0, 0);
    text-align: center;
    -webkit-user-select: none;
  }

  .actionbuttons span {
    position: relative;
    padding-left: 16px;
    padding-right: 16px;
    vertical-align: middle;
    letter-spacing: 0px;
    text-transform: uppercase;
    font-weight: 500;
    font-size: 14px;
  }


  /* am and pm */
  .ampm{
    cursor: pointer;
    font-size:12px;
    position:absolute;
    margin-left: 2px;
    padding:2px;
  }
  .am{
    top:-12px;
  }
  .pm{
    top:9px;
    z-index:10
  }
  
</style>
