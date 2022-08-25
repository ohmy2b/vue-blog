<template>
    <el-dialog title="评论" width="61%" v-model="state.dialogDodel" @close="cancel">
        <el-form>
            <el-forn-item>
                <el-input type="textarea" v-model="state.content" placeholder="文明评论" autocomplete="off"></el-input>
            </el-forn-item>
        </el-form>
    <div slot="footer" class="dialog-footer">
        <el-button type="default" @click="cancel">取消</el-button>
        <el-button type="primary" @clcik="submit">提交</el-button>
    </div>
    </el-dialog>
</template>

<script lang="ts">
import { propsToAttrMap } from "@vue/shared";
import { ElMessage } from "element-plus";
import { type } from "os";
import { isContext } from "vm";
import { defineComponent, reactive, watch } from "vue";
import service from "../utils/https";
import urls from "../utils/urls";

export default defineComponent({
    name:"Comment",
    props:{
        visible:{
            type:Boolean,default:false,
        },comment_id:{
            type:String,default:''
        },article_id: {
            type: String,default: "",
        },to_user:{
            default:{}
        },
    },
    emits:["ok","cancel"],
    setup(props,context){
        const state=reactive({
            dialogDodel:props.visible,
            btnLoading:false,
            content:"",
            cacheTime:0,
            times:0,
        });
        const cancel=():boolean=>{
            context.emit("cancel",false);
            return false
        };
        const submit=async():Promise<void>=>{
            if(!props.article_id){
                ElMessage({
                    message:"文章不存在",
                    type:"error"
                });
                return;
            }
            if(state.times>2){
                ElMessage({
                    message:"今日评论次数已用尽",
                    type:"warning"
                });
                return;
            }
        let nowTime=new Date().getTime()
        if(nowTime-state.cacheTime<4000){
            ElMessage({
                message:"评论间隔时间过短,请稍后再尝试",
                type:"warning"
            })
            return;
        }
        if(!state.content){
            ElMessage({
                message:"评论内容不能为空",
                type:"error"
            })
            return;
        }
        let user_id = "";
        if (window.sessionStorage.userInfo) {
            let userInfo = JSON.parse(window.sessionStorage.userInfo);
            user_id = userInfo._id;
        } else {
            ElMessage({
            message: "登录才能评论，请先登录！",
            type: "warning",
            });
            return;
        }
        state.btnLoading=true;
        await service.post(urls.addThirdComment,{
            article_id: props.article_id,
            user_id,
            comment_id: props.comment_id,
            to_user: JSON.stringify(props.to_user),
            content: state.content,
        });
        state.btnLoading=false
        state.times++
        state.cacheTime=nowTime
        state.content=""
        context.emit("ok",false)
        ElMessage({
            message:"评论有效",
            type:"success"
        });
    };
        watch(props,(val,oldval)=>{
            state.dialogDodel=val.visible
        });
        return {state,cancel,submit};
    }
})
</script>
<style scoped>
.dialog-footer {
  text-align: right;
}
</style>
