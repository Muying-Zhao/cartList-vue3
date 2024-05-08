<template>
    <div class="wrapper">
        <div class="search">
            <div 
                class="search__back iconfont"
                @click="handleBackClick"    
            >&#xe7e0;</div>
            <div class="search__content">
                <span class="search__content__icon iconfont">&#xe626;</span>
                <input  
                   class="search__content__input"
                   placeholder="请输入商品名称"    
               />
            </div>
        </div>
        <!-- v-show="item.imgUrl使该页面展示时若页面未显示ShopInfo组件也不忙展示，可以阻止图片晚加载出来 -->
        <ShopInfo :item="item" :hideBorder="true" v-show="item.imgUrl"/>
        <Content :showName="item.name"/>
        <Cart/>
    </div>
</template>

<script>
import {  reactive, toRefs} from "vue";
import { useRouter,useRoute } from "vue-router";
import { get } from "../../utils/request";
import ShopInfo from "../../components/ShopInfo";
import Content from "./Content";
import Cart from "./Cart";

// 获取当前商铺信息
const useShopInfoEffect=()=>{
    // route指的是当前访问路径的信息
    const route=useRoute();
    // console.log(route.name,route.params);
    const data=reactive({item:{}})
    const getItemData=async()=>{
        const result=await get(`api/shop/${route.params.id}`)
        if(result?.errno===0&&result?.data){
            data.item=result.data;
        }
        
    }
    const {item}=toRefs(data);
    return {item,getItemData}
}
// 点击回退逻辑
const useBackRouterEffect=()=>{
    // router指的是当前整个大的路由信息
    const router=useRouter();
    const handleBackClick=()=>{
        router.back();
    }
    return handleBackClick
}

export default{
   name:'Shop',
   components:{ShopInfo,Content,Cart},
   setup(){
    const {item,getItemData}=useShopInfoEffect();
    const handleBackClick=useBackRouterEffect();
    getItemData();
    return {item,handleBackClick}
   }
}

</script>

<style lang="scss" scoped>
@import '../../style/viriables.scss';
.wrapper{
    padding: 0 .18rem;
}
.search{
    margin: .14rem 0 .04rem 0;
    display: flex;
    line-height: .32rem;
    &__back{
        font-size: .2rem;
        width: .3rem;
        color: #B6B6B6;
    }
    &__content{
        display: flex;
        flex:1;
        background: $search-bgColor;
        border-radius: .16rem;
        &__icon{
            width: .44rem;
            height: .32rem;
            text-align: center;
            color: $search-fontColor;
        }
        &__input{
            padding-right: .22rem;
            display: block;
            width: 100%;
            height: .32rem;
            line-height: .32rem;
            border: none;
            outline: none;
            background: none;
            color: $content-fontcolor;
            font-size: .14rem;
            &::placeholder{
                color: $content-fontcolor;
            }
        } 
    }
}

</style>