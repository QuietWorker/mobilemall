<template>
  <div class="details">
    <details-nav-bar class="navbar"  @themeClick="themeIndex" :Index="ScrollIndex"></details-nav-bar>  
    <scroll
        class="content"
        ref="scroll"
        @position='getPosition'
        :probe-type='3'>
        <details-swiper :top-images="topImages"></details-swiper>
        <details-base-info :goods="goodsInfo"></details-base-info>
        <details-shop-info :shop="shopsInfo"></details-shop-info>
        <details-goods-info :detailInfo="detailsGoodsInfo" ref="detailInfo"></details-goods-info>
        <details-param-info :paramInfo="paramInfo" ref="paramInfo"></details-param-info>
        <details-comment-info :comments="commentInfo" ref="comments"></details-comment-info>
        <goods-list :goods="recommends" ref="recommend"></goods-list>
        <back-top @click.native="back2top" v-show="isback" />
    </scroll>
        

  </div>
</template>

<script>

import DetailsNavBar from './childComponents/DetailsNavBar.vue';
import DetailsBaseInfo from './childComponents/DetailsBaseInfo.vue'
import DetailsShopInfo from './childComponents/DetailsShopInfo.vue'
import DetailsGoodsInfo from './childComponents/DetailsGoodInfo.vue'
import DetailsParamInfo from './childComponents/DetailsParamInfo.vue'
import DetailsCommentInfo from './childComponents/DetailsCommentInfo.vue'

//公用组件
import Scroll from 'components/common/scroll/Scroll.vue';
import GoodsList from 'components/content/goods/GoodsList.vue'

import {getDetailsData,getRecommend,GoodsInfo,ShopInfo,GoodParam,Comment} from 'network/detail.js'
import {debounce} from 'components/common/utils.js'
import { BackTopMixin } from "common/mixin.js";

import DetailsSwiper from './childComponents/DetailsSwiper.vue';


export default {
    name:"Details",
    components:{
        DetailsNavBar,
        DetailsSwiper,
        DetailsBaseInfo,
        DetailsShopInfo,
        DetailsGoodsInfo,
        DetailsParamInfo,
        DetailsCommentInfo,
        Scroll,
        GoodsList
    },
    data(){
        return{
            iid:null,
            topImages:[],
            goodsInfo:{},
            shopsInfo:{},
            detailsGoodsInfo:{},
            paramInfo:{},
            commentInfo:{},
            recommends:[],
            themeTopYs: [],
            ScrollIndex:0,
        }
    },
    mixins: [BackTopMixin],
    created(){
        //1.保存传入的iid
        this.iid=this.$route.params.iid
        //2.根据iid请求相应的详情数据
        getDetailsData(this.iid).then(res => {
            //1.获取顶部图片数据
            const data = res.result
            this.topImages=data.itemInfo.topImages
            //2.获取商品信息
            this.goodsInfo= new GoodsInfo(data.itemInfo,data.columns,data.shopInfo.services)
            //3.获取店铺信息
            this.shopsInfo= new ShopInfo(data.shopInfo)
            //4.获取商品详细信息
            this.detailsGoodsInfo= data.detailInfo

            this.paramInfo=new GoodParam(data.itemParams.info,data.itemParams.rule)

            const commentData = data.rate.list;
            this.commentInfo = new Comment(commentData);    
        
        })
    
        //3.请求推荐数据
        getRecommend().then(res=>{
            this.recommends=res.data.list
        })
        },
        mounted(){
            const fn = debounce(this.$refs.scroll.refresh, 400);
            this.$bus.$on("detailImageLoad", () => {
                fn();
            });
        },
         methods:{
        // 获取当前点击的主题
            themeIndex(currentIndex) {
            // 点击跳转
            this.$refs.scroll.scrollTo(0, -this.themeTopYs[currentIndex], 100);
            },
            // 滚动改变对应主题
            getPosition(position) {
            position = -position.y;
            this.isShowBackTop(position);
            // console.log(this.ScrollIndex);
            for (let i = 0; i < this.themeTopYs.length; i++) {
                if (position >= this.themeTopYs[i]) this.ScrollIndex = i;
            }
            },
        }
}
</script>

<style scoped>
    .details {
        position: relative;
        z-index: 10;
        height: 100vh;
        background: #fff;
    }
    .navbar{
        background-color: #fff;
		position: relative;
		top: 0;
		left: 0;
		right: 0;
		z-index: 11;
    }
    .content {
        position: absolute;
        overflow: hidden;
        top: 44px;
        bottom: 49px;
        left: 0;
        right: 0;
    }

</style>
