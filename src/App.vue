<template>
  <div id="app">
     <div>
       {{time}}
       <dateTime v-model = "time" type="time" :aspect="a1"/>
     </div>
     <div style="position:absolute;left:600px">
       {{time1}}
       <dateTime v-model = "time1" type="date" :aspect="a1"/>
     </div>
      <div style="position:absolute;left:1200px">
        {{time2}}
       <dateTime v-model = "time2" type="dateTime" :aspect="a1"/>
     </div>
     <!-- <arrayCopy/> -->
      <!-- <table1/> -->
  </div>
</template>

<script>

/* Import Bootstrap Vue Styles */
// import jquery from "jquery"

import moment from "moment";
import dateTime from "./components/dateTime.vue";
import arrayCopy from "./components/arrayCopy.vue";
import table1 from "./components/table.vue";
export default {
  components: {
    dateTime,
    arrayCopy,
    table1
 },
 computed:{
   a1(){
     return {
      enable: true,
      visible: true,
      editable: true,
      clickable: true
     }
   },
    a2(){
     return {
      enable: false,
      visible: true,
      editable: false,
      clickable: false
     }
   }
 },
 data() {
    return {
      time:'12:00',
      time1: "",
      time2: ''
    };
  }
};
</script>
