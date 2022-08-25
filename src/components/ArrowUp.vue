<template>
    <div>
        <i v-show="isShowBtn" @click="topFun" class="el-icon-arrow-up"></i>
    </div>
</template>

<script lang="ts">
import{defineComponent,reactive,onMounted} from "vue";
export default defineComponent({
    name:'ArrowUp',
    setup(){
        const state=reactive({
            isShowBtn:false
        });
        onMounted(()=>{
            window.onscroll=():void=>{
                if(window.document.body.scrollTop>100||window.document.documentElement.scrollTop>100){
                    state.isShowBtn=true
                }else {
                    state.isShowBtn=false
                }
            }
        });
        const topFun=():void=>{
            document.body.scrollTop=0
            document.documentElement.scrollTop=0
        }
        return {state,topFun}
    }
})
</script>
<style scoped>
.el-icon-arrow-up {
  position: fixed;
  bottom: 50px;
  right: 40px;
  z-index: 99;
  border: none;
  outline: none;
  background-color: rgb(119, 118, 118);
  cursor: pointer;
  padding: 15px;
  color: white;
  border-radius: 50%;
}

.el-icon-arrow-up:hover {
  background-color: #555;
}
</style>