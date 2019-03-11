<template>
  <div id="app">
     <div class="wrapper">
      <div class='col-sm-6'>
        <div class="form-group" >
          <div class='input-group date' id='datetimepicker2' >
            <input type='text' 
              @input ="onInput"
              @blur = "onBlur"
              class="form-control"
              v-model ="dateTime"
              :class="inputClass"/>
            <span class="input-group-addon" @click="handlerClick($event)">
              <i class="fa fa-calendar"></i>
            </span>
          </div>
          
        </div>
      </div>
   
    <div class="dateTimePicker" v-if="!isError && isShowPicker">
      <HelloWorld v-model="date"/>
      <time-picker2 v-model ="time"/>
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
import HelloWorld from "./components/HelloWorld.vue";
import timePicker2 from "./components/timePicker2.vue";
export default {
  components: {
    HelloWorld,
    timePicker2
  },
  props: {
    value: {
      type: String,
      default: moment().format("YYYY-MM-DD HH:mm")
    }
  },
  computed: {
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
      date: moment(this.value).format("YYYY-MM-DD"),
      time: moment(this.value).format("HH:mm"),
      dateFormat: "YYYY-MM-DD",
      timeFormat: "HH:mm",
      isError: false,
      oldV: this.value
    };
  },
  watch: {
    date(val) {
      this.dateTime = val + " " + this.time;
    },
    time(val) {
      this.dateTime = this.date + " " + val;
    },
    dateTime(val, oldV) {
      if (val.indexOf(" ") === -1) {
        // 删除空格
        this.isError = true;
      } else {
        this.date = val.split(" ")[0];
        this.time = val.split(" ")[1];

        // validate
        if (val && !moment(val).isValid()) {
          //  如果时间格式错误
          this.isError = true;
          this.oldV = oldV;
        } else {
          this.isError = false;
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
    onBlur() {
      this.dateTime = moment(this.isError ? this.oldV : this.dateTime).format(
        "YYYY-MM-DD HH:mm"
      );
    },
    onInput() {
      // this.dateTime = this.date + " " + this.time
    },
    handlerClick(e){
      this.isShowPicker = !this.isShowPicker;
      //  如果datetimePikcer的左边距 加上  datetimePicker的宽度  大于  可视区的宽度 并且 可视区的宽度  大于  datetimePicker的宽度   
      // 则显示在左边
      let pickerLeft = document.querySelector(".wrapper").offsetLeft;
      let pickerWidth = document.querySelector(".datePickerWrapper").offsetWidth + document.querySelector(".timeWrapper").offsetWidth
      let clientWidth = document.body.clientWidth;
      if(clientWidth > pickerWidth && pickerWidth + pickerLeft > clientWidth){
        console.log("在左边")
      }
    }
  }
};
</script>
<style scoped>
.wrapper {
  position: absolute;
  margin-left:600px;
}
.date {
  width: 200px;
}
.error {
  border: 1px solid red;
}
</style>