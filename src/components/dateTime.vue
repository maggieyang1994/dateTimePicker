<template>
  <div id="app">
     <div class="wrapper">
        <div class='col-sm-6'>
            <div class="form-group" >
            <div class='input-group date' id='datetimepicker2' >
                <input type='text' 
                @blur = "onBlur($event)"
                class="form-control"
                v-model ="dateTime"
                :class="inputClass"/>
                <span class="input-group-addon" @click="handlerClick($event)">
                <i class="fa fa-calendar"></i>
                </span>
            </div>
            
            </div>
        </div>
   
        <div class="dateTimePicker" v-if="!isError && isShowPicker && type === 'dateTime'">
        <HelloWorld v-model="date"/>
        <time-picker2 v-model ="time"/>
        </div> 
        <div class="dateTimePicker" v-if="!isError && isShowPicker && type === 'date'">
        <HelloWorld v-model="dateTime"/>
       
        </div> 
        <div class="dateTimePicker" v-if="!isError && isShowPicker && type === 'time'">
            <time-picker2 v-model ="dateTime" class ="typeTime"/>
        </div> 
    </div>
    
  </div>
</template>

<script>
import "font-awesome/css/font-awesome.min.css";
import "simple-line-icons/css/simple-line-icons.css";
/* Import Bootstrap Vue Styles */
// import jquery from "jquery"
const jQuery = require("jquery");
import moment from "moment";
import HelloWorld from "./HelloWorld.vue";
import timePicker2 from "./timePicker2.vue";
export default {
  components: {
    HelloWorld,
    timePicker2
  },
  props: {
    value: {
      type: String
    },
    type: {
      type: String,
      default: "dateTime",
      validator(value) {
        return value === "date" || value === "time" || value === "dateTime";
      }
    },
    aspect: {
      type: Object,
      default: function() {
        return {
          enable: true,
          visible: true,
          editable: true,
          clickable: true
        };
      }
    }
  },
  computed: {
    format(){
        if(this.type === 'time') return "HH:mm";
        else if(this.type === 'date') return "YYYY-MM-DD";
        else return 'YYYY-MM-DD HH:mm'
    },
    localValue(){
        if(this.value){
            return this.type === 'time'? '2012-12-12 ' + this.value : this.value
        }
    },
    inputClass() {
      return {
        error: this.isError
      };
    }
  },
  data() {
    return {
      isShowPicker: false,
      dateTime: this.value,
      date: this.value && moment(this.localValue).format("YYYY-MM-DD"),
      time: this.value && moment(this.localValue).format("HH:mm"),
      isError: false,
      oldV: this.value
    };
  },
  watch: {
    date(val) {
      this.dateTime = this.type === 'dateTime' ? val + " " + this.time : val
    },
    time(val) {
      this.dateTime = this.type === 'dateTime' ? this.date + " " + val : val
    },
    dateTime(val, oldV) {
      if (val) {
        if(this.type === 'dateTime'){
            if (val.indexOf(" ") === -1) {
                this.isError = true;
                this.oldV = oldV;
            } else {
                this.date = val.split(" ")[0];
                this.time = val.split(" ")[1];
                !moment(val).isValid() && (this.oldV = oldV);
                this.isError = !moment(val).isValid();
            }
        }
        else if(this.type === 'date'){
             !moment(val).isValid() && (this.oldV = oldV);
             this.isError = !moment(val).isValid();
        }else{
           !moment('2012-12-12 ' + val).isValid() && (this.oldV = oldV);
            this.isError = !moment('2012-12-12 ' + val).isValid(); 
        }
        
      }
    }
  },
  mounted() {
    // 点击空白处关闭
    var self = this;
    jQuery(document).click(function(e) {
      if (
        !jQuery(e.target).parents(".dateTimePicker").length &&
        !jQuery(e.target).parents(".input-group-addon").length
      ) {
        self.isShowPicker = false;
      }
    });
  },
  methods: {
    onBlur(e) {
      if (e.target.value)
        this.dateTime = moment(this.isError ? (this.type === 'time' ?'2012-12-12 ' + this.oldV : this.oldV) : (this.type==='time' ? '2012-12-12 ' + this.dateTime: this.dateTime)).format(this.format);
    },
    handlerClick() {
      // 只能同时打开一个
      let dom = document.querySelector(".dateTimePicker");
      dom && (dom.style.display = "none");
      this.isShowPicker = !this.isShowPicker;
      //  如果datetimePikcer的左边距 加上  datetimePicker的宽度  大于  可视区的宽度 并且 可视区的宽度  大于  datetimePicker的宽度
      // 则显示在左边
      // let pickerLeft = document.querySelector(".wrapper").offsetLeft;
      // let pickerWidth = document.querySelector(".datePickerWrapper").offsetWidth + document.querySelector(".timeWrapper").offsetWidth
      // let clientWidth = document.body.clientWidth;
      // if(clientWidth > pickerWidth && pickerWidth + pickerLeft > clientWidth){
      //   console.log("在左边")
      // }
    }
  }
};
</script>
<style scoped>
.wrapper {
  position: absolute;
  /* margin-left:600px; */
}
.date {
  width: 200px;
}
.error {
  border: 1px solid red;
}
.typeTime{
    left :15px;
    top:-11px
}
</style>