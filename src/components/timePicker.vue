<template>
<div  v-if="aspect.visible">
  <label v-if="label" :for="safeId()" :class="Object.keys(this.errorList).length>0 && aspect.enable && aspect.editable ? 'rais-err-msg' : ''">
    {{label | lang}} 
    <i v-if="Object.keys(this.errorList).length>0 && aspect.enable && aspect.editable"
        class="fa fa-exclamation-circle" 
        v-b-tooltip.hover
        :title="errorList[Object.keys(this.errorList)[0]]"/>
  </label>
  <span v-show="aspect.editable">
    <div  class='input-group date'>
            <input 
            type='text' 
            class="form-control" 
            v-model ="picker"
            :disabled="!aspect.enable" 
            :readonly="!aspect.editable"
            
           />
            <span :disabled="!aspect.clickable" class="input-group-addon" style="display:block" @click="(e) => {isShowPiker = !isShowPiker;e.preventDefault()}">
                <span class="fa fa-calendar"></span>
            </span>
    </div>
  </span>
  <div v-if="isShowPiker" class='pickerWrapper' >
    <v-app>
      <div v-if ='type === "dateTime"'>
          <rais-card no-body style="max-width: 292px">
              <rais-tabs card>
                  <rais-tab no-body title ="Date">
                      <v-date-picker v-model="datePicker" width ="290" scrollable>
                      </v-date-picker>
                  </rais-tab>
                  <rais-tab no-body title ="Time">
                      <v-time-picker v-model="timePicker" width ="290" scrollable>
                      </v-time-picker>
                  </rais-tab>
                  <template slot="tabs">
                      <b-nav-item href="#" @click="() => {isShowPiker = false}">Close</b-nav-item>
                  </template>
              </rais-tabs>
          </rais-card>
      </div>
      <div v-else-if ='type === "date"'>
        <v-date-picker v-model="datePicker" width ="290" scrollable  @input ="() => {isShowPiker = false}">
        </v-date-picker>
      </div>
      <div v-else>
        <v-time-picker v-model="timePicker" width ="290" scrollable @input ="() => {isShowPiker = false}">
        </v-time-picker>
      </div>
    </v-app>
  </div>
  
</div>
   
</template>

<script type="text/javascript">
import { idMixin, formMixin, formSizeMixin, formStateMixin } from "../mixins";
import ComponentMixin from "@/plugins/componentMixin";
// import{ VDatePicker, VTimePicker} from "vuetify/lib"
const jQuery = window.jQuery || require("jquery");
const moment = window.moment || require("moment");
export default {
  name: "rais-dateTimePicker2",
  mixins: [idMixin, formMixin, formSizeMixin, formStateMixin, ComponentMixin],
  // components: { VDatePicker, VTimePicker},
  props: {
    label: String,
    value: {
      default: null,
      required: true,
      validator(value) {
        return (
          value === null ||
          value instanceof Date ||
          typeof value === "string" ||
          value instanceof String ||
          value instanceof moment
        );
      }
    },
    type: {
      type: String,
      default: "dateTime",
      validator(value) {
        return value === "date" || value === "time" || value === "dateTime";
      }
    },

    timezone: {
      type: [String, Number],
      default: "0"
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
    localValue(){
      if(this.value) {
        let val = this.type === "time" ? '2012-12-12 ' + this.value : this.value
        return moment(val).format(this.format)
      }
    },
    format() {
      return this[this.type + 'Format']
    },
    picker: {
      get() {
        return moment(this.datePicker + " " + this.timePicker).format(this.format);
      },
      set(value) {
        if(moment(value).format(this.format) === "Invalid date"){
          this.setError( "0020V", this.$store.state.sys.staging.sysMsgMap["0020V"])
        } else {
          this.datePicker = moment(value).format(this.dateFormat);
          this.timePicker = moment(value).format(this.timeFormat);
          this.setError("0020V", "");
         }
        }
      }
    },
  data() {
    return {
      isShowPiker: false,
      dateFormat: "YYYY-MM-DD",
      timeFormat: "HH:mm",
      dateTimeFormat: "YYYY-MM-DD HH:mm",
      // picker: null,
      datePicker: moment(this.localValue).format("YYYY-MM-DD"),
      timePicker: moment(this.localValue).format("HH:ss")
    };
  },
  methods: {
    // onInput(e) {
    //   let time;
    //   time = (this.type === 'time' || this.type === 'timeWithSec') ? moment('2018-12-12 ' + e.target.value).format( this.format) : moment(e.target.value).format(this.format);
    //   if(e.target.value && time === "Invalid date"){
    //     this.setError('0020V', this.$store.state.sys.staging.sysMsgMap['0020V'])
    //   }else{
    //     this.setError('0020V', '')
    //   }
    // },
  },
  mounted(){
    var self = this
    $(document).mouseup(function(e){
      if($(e.target).parents(".pickerWrapper").length === 0 && e.target.className !== 'form-control'){
        self.isShowPiker = false
      }
    })
  }
}

</script>

<style scoped>
.tabs .navWidth {
  width: 290px;
}
.tab-content .tab-pane {
  padding: 0;
}
.tab-content .card-body {
  padding: 1.25rem;
}
.pickerWrapper {
  position: absolute;
  z-index: 99999;
}
.pickerWrapper .application--wrap {
  min-height: 400px;
}
</style>