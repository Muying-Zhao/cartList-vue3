<template>
  <div class="wrapper">
    <img
      src="http://www.dell-lee.com/imgs/vue3/user.png"
      alt=""
      class="wrapper__img"
    />
    <div class="wrapper__input">
      <input
        class="wrapper__input__content"
        placeholder="用户名"
        v-model="username"
      />
    </div>
    <div class="wrapper__input">
      <!-- 如果不想输入时被上次输入内容占空，
        给input标签加上autocomplete="new-password" -->
      <input
        class="wrapper__input__content"
        type="password"
        placeholder="请输入密码"
        v-model="password"
      />
    </div>
    <div class="wrapper__login-button" @click="handleLogin">登陆</div>
    <!-- <router-link :to="{name:'Register'}">
            <div class="wrapper__login-link">立即注册</div>
        </router-link> -->
    <!-- 之所以没用router-link组件是因为它会自动给外面加一个a标签，使其有下滑线 -->
    <div class="wrapper__login-link" @click="handleRegisterClick">立即注册</div>
    <Popup v-if="show" :message="toastMessage" />
  </div>
</template>

<script>
import { reactive,toRefs } from "vue";
import { useRouter } from "vue-router";
// 安装完工具npm install axios --save后开始引入
import { post } from "../../utils/request";
import Popup,{useToastEffect} from "../../components/Popup";
// 后端要求，已在函数封装里实现
// axios.defaults.headers.post['Content-Type'] = 'application/json';
// 处理注册逻辑
const useLoginEffect=(showToast)=>{
    const router=useRouter();
    const data = reactive({
            username:'',
            password:'',
        })
    /*
        const handleLogin=()=>{
            axios.post('/api/user/login',{
                username: data.username,
                password: data.password
            }).then(()=>{
                localStorage.isLogin=true;
                router.push({name:'Home'})
            })
            .catch(()=>{
                alert('登陆失败');
            });
        } 
    */
    const handleLogin=async()=>{
        try{
            const result=await post('/api/user/login',{
                username: data.username,
                password: data.password
            })
            if(result?.errno===0){
                localStorage.isLogin=true;
                router.push({name:'Home'})
            }else{
                showToast('登陆失败')
            }
        }catch(e){
            showToast('请求失败')
        }
    }
    const {username,password}=toRefs(data)
    return{username,password,handleLogin}
}
// 处理注册跳转
const useRegisterEffect=()=>{
    const router=useRouter();
    const handleRegisterClick=()=>{
        router.push({name:'Register'})
    }
    return {handleRegisterClick}
}

export default {
    name:'Login',
    components:{Popup},
    // setup的职责就是告诉你代码执行的一个流程
    setup(){
        // 弹窗逻辑
        const {show,toastMessage,showToast}=useToastEffect();
        //登陆逻辑    
        const {username,password,handleLogin}=useLoginEffect(showToast)
        // 注册逻辑
        const {handleRegisterClick}=useRegisterEffect()
        return {handleLogin,handleRegisterClick,show,toastMessage,username,password}
    }
}
</script>

<style lang="scss" scoped>
@import "../../style/viriables.scss";
.wrapper {
  position: absolute;
  top: 50%;
  left: 0;
  right: 0;
  transform: translateY(-50%);
  &__img {
    display: block;
    margin: 0 auto 0.4rem auto;
    width: 0.66rem;
    height: 0.66rem;
  }
  &__input {
    height: 0.48rem;
    margin: 0 0.4rem 0.16rem 0.4rem;
    padding: 0 0.16rem;
    background: #f9f9f9;
    border: 0.01rem solid rgba(0, 0, 0, 0.1);
    border-radius: 0.06rem;
    &__content {
      margin-top: .12rem;
      border: none;
      outline: none;
      line-height: 0.22rem;
      width: 100%;
      background: none;
      font-size: 0.16rem;
      color: $content-notice-fontcolor;
      &::placeholder {
        color: $content-notice-fontcolor;
        line-height: .24rem;
      }
    }
  }
  &__login-button {
    margin: 0.32rem 0.4rem 0.16rem 0.4rem;
    line-height: 0.48rem;
    background: $btn-bgColor;
    box-shadow: 0 0.04rem 0.08rem 0 rgba(0, 145, 255, 0.32);
    border-radius: 0.04rem;
    color: $bgColor;
    font-size: 0.16rem;
    text-align: center;
  }
  &__login-link {
    text-align: center;
    font-size: 0.14rem;
    color: $content-notice-fontcolor;
  }
}
</style>