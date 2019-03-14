<template>
    <div>
        <ul>
            <li v-for="(item,key) in computedList" :key ="key">
                <span class="span" >{{item.a}}</span>
                <span class="span" @click ="handleClick(item, key)">{{item.b}}</span>
                <span class="span" >{{item.c}}</span>
            </li>
        </ul>
        <pre>computedList: {{computedList}}</pre>  <br/>  
        <pre>list: {{list}}</pre>  
        
</div>
</template>
<script>
import Vue from 'vue'
export default {
   data(){
       return {
           list: [{a:1,b:2}]
       }
   },
   computed:{
       computedList(){
           // 如果这里是深拷贝  无论a还是b的改变都触发不了 list的改变
         // 浅拷贝
         this.list.forEach(x => x.c = false);
         return this.list
       }
   },
   methods:{
       handleClick(item, key){
            // 之所以改a就会触发computedList 是因为 改变了list  然后computedList就改变了
                // item.a = !item.a;
            // 直接修改计算属性的值  计算属性的值是不会更新的 这里的computedList 只有在list变了才会变
                // item.c = !item.c;
            // Vue.set(this.computedList[key], "c", !item.c)
            

            item.c = !item.c;
            console.log(this.list);
            console.log(this.computedList)
          

        }
   }
}
</script>
<style>
.span{
    display:inline-block;
    width:50px;
    height:50px;
    border:2px solid red;
    margin-right:20px
}

</style>
