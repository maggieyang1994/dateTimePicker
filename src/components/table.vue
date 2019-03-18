<template>
  <div>
     <!--  这里focus blur 和 focusin focusout的区别 -->
     <!-- focus blur:不支持冒泡    focusin  focusout: 支持冒泡 -->
     <!-- 如果td绑定的是 focus  不会出发table的 focus事件   focusin可以 -->
      <table @focusin="focusin"  @focusout="focusout" @click="rowClick" @keyup="keyup" @keydown="keydown" border="1">
        
        <tr on>
            <td>1</td>
            <td>1</td>
            <td>1</td>
            <!-- 默认情况下 只有 表单元素有focus事件， 但是如果给dom 设置  contentEditable = true  或者是  tableIndex !== -1 的值 就变成了focusable的元素-->
            <!-- <td><span  @click="onClick($event)" contenteditable="false" tabindex="">click me</span></td> -->
            <td><input type="button"  value="click me" @click="onClick($event)" tabindex="-1" readonly disabled/></td>
        </tr>
    </table>
  </div>
    
</template>
<script>
export default {
    name:"table",
    data(){
        return {

        }
    },
    methods:{
        focusin(){
            console.log('focusin')
        },
        focusout(){
            console.log('focusout')
        },
        rowClick(){
            console.log('rowClick')
        },
        keyup(){
            console.log('keyup')
        },
        keydown(){
            console.log('keydown')
        },
        onClick(e){
            // e.cancelable = false;
            // e.stopPropagation()
            // e.cancelBubble = true;
            // e.preventDefault();
            console.log('col click')
        }
    }
}
</script>

