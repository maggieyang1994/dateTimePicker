<template>
  <div id="app">
     <div class="wrapper">
      <div class='col-sm-12'>
      <div class="form-group" >
        <div class='input-group date' id='datetimepicker2' >
          <input type='text' class="form-control" v-model ="dateTime" @input='onInput($event)' :class="inputClass"/>
          <span class="input-group-addon" @click="isShowPicker = !isShowPicker">
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
const jQuery = require("jquery")
import moment from "moment";
import HelloWorld from "./components/HelloWorld.vue";
import timePicker2 from "./components/timePicker2.vue";
export default {
  components: {
    HelloWorld,
    timePicker2
  },
  props: {
    value:{
      type: String,
      default: moment("2012-12-12 10:30").format("YYYY-MM-DD HH:mm")
    }
  },
  computed: {
    inputClass(){
      return {
        'error': this.isError
      }
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
      isError: false
    };
  },
  watch: {
    date(val){
      this.dateTime = val + " " + this.time
    },
    time(val){
      this.dateTime = this.date + " " + val
    },
    dateTime(val){
      this.date  = val.split(" ")[0];
      this.time = val.split(" ")[1];
    }
  },
  mounted(){
    // 点击空白处关闭
    var self = this;
    jQuery(document).click(function(e){
      if(!jQuery(e.target).parents('.dateTimePicker').length && !jQuery(e.target).parents(".input-group-addon").length){
        self.isShowPicker = false
      }
    })
  },
  methods: {
    onInput(e){
      if(e.target.value && !moment(e.target.value).isValid()){
        //  如果时间格式错误
        this.isError = true;
      }else{
        this.isError = false
      }
    }
  }
};
</script>
<style scoped>
.wrapper {
  position: absolute;
}
.date{
  width:200px
}
.error{
  border:1px solid red
}
</style>