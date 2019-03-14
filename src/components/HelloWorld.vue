<template>
<div class="datePickerWrapper">
   <div class='input-group date' id='datetimepicker2' style="visibility :hidden">
          <input type='text' class="form-control" />
          <span class="input-group-addon" >
            <i class="fa fa-calendar"></i>
          </span>
    </div>
    <div class="pickerHeader">
            <div class="pickerHeader-title">
              <div class="picker__title__btn date-picker-title__year">{{date | filterYear}}</div>
              <transition name ="fade">
                 <div class="picker__title__btn date-picker-title__date picker__title__btn--active"  ><div>{{date | filterDate}}</div></div>
              </transition>  
            </div>
    </div>
</div>

 </template>


<script>
const jQuery = require("jquery")
import moment from "moment";
// import 'bootstrap-vue/dist/bootstrap-vue.css'

// You have to import css yourself
import "../styles/css/bootstrap.min.css";
import "../styles/css/bootstrap-datetimepicker.min.css";
import "../styles/css/bootstrap-datetimepicker-standalone.css";
import "../styles/css/bootstrap-glyphicons.css";
import "vue-bootstrap-datetimepicker";
import "../styles/css/custom.css";
const events = ["hide", "show", "change", "error", "update"];
export default {
  name: "date-picker",
  props: {
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
    // http://eonasdan.github.io/bootstrap-datetimepicker/Options/
    config: {
      type: Object,
      default: () => ({
        format: "YYYY-MM-DD",
        keepOpen: true
      })
    },
    /**
     * You can set this to true when component is wrapped in input-group
     * Note: inline and wrap mode wont work together
     */
    wrap: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      dp: null,
      // jQuery DOM
      elem: null,
      isShowDatePicker: false,
      date:this.value
    };
  },
  mounted() {
    // Return early if date-picker is already loaded
    /* istanbul ignore if */
    if (this.dp) return;
    // Handle wrapped input
    this.elem = jQuery(this.wrap ? this.$el.parentNode : this.$el);
    // Init date-picker
    this.elem.datetimepicker(this.config);
    // Store data control
    this.dp = this.elem.data("DateTimePicker");
    // Set initial value
    this.dp.date(this.value);
    // Watch for changes
    this.elem.on("dp.change", this.onChange);
    // Register remaining events
    this.registerEvents();
    this.dp.show()
    
  },
  watch: {
    /**
     * Listen to change from outside of component and update DOM 
     *
     * @param newValue
     */
    value(newValue) {
      this.dp && this.dp.date(newValue || null);
      this.$emit("input", newValue)
      this.date = newValue
    },
    /**
     * Watch for any change in options and set them
     *
     * @param newConfig Object
     */
    config: {
      deep: true,
      handler(newConfig) {
        this.dp && this.dp.options(newConfig);
      }
    }
  },
  methods: {
    /**
     * Update model upon change triggered by date-picker itself
     *
     * @param event
     */
    onChange(event) {
      let formattedDate = event.date
        ? event.date.format(this.dp.format())
        : null;
      this.date = formattedDate
      this.$emit("input", formattedDate);
    },
    /**
     * Emit all available events
     */
    registerEvents() {
      events.forEach(name => {
        this.elem.on(`dp.${name}`, (...args) => {
          this.$emit(`dp-${name}`, ...args);
        });
      });
    },
    clickCalendar() {
      this.isShowDatePicker = !this.isShowDatePicker;
      this.isShowDatePicker ? this.dp.show() : this.dp.hide();
    }
  },
  /**
   * Free up memory
   */
  beforeDestroy() {
    /* istanbul ignore else */
    if (this.dp) {
      this.dp.destroy();
      this.dp = null;
      this.elem = null;
    }
  },
  filters:{
    filterDate: function(date){
      // if(!date) return '';
      // return moment(date).format('ddd, MMM DD')
      return moment(date ? date :moment()).format('ddd, MMM DD')
    },
    filterYear: function(date){
      // if(!date) return '';
      // return moment(date).format('YYYY')
      return moment(date ? date :moment()).format('YYYY')
    }
  }
  
};
</script>

