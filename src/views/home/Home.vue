<template>
  <div id="home">
     <nav-bar class="home-nav"><div slot="center">购物街</div></nav-bar>
    <scroll class="content" 
            ref="scroll" 
            :probe-type="3" 
            @scroll="contentScroll"
            :pull-up-load="true"
            @pullingUp="loadMore"> 
      <home-swiper :banners="banners"></home-swiper>
      <home-recommend-view :recommends="recommends"></home-recommend-view>
      <feature-view></feature-view>
      <tab-control class="tab-control" :titles="['最新','精选','热门']" @tabClick='tabClick'></tab-control>
      <goods-list :goods="showGoods"></goods-list>
    </scroll>
    <back-top @click.native="backClick" v-show="isShowBackTop"></back-top> //组件事件的监听要用.native
    <ul>
    </ul>
  </div>
</template>

<script>
import NavBar from 'components/common/navbar/NavBar.vue'
import Scroll from 'components/common/scroll/Scroll.vue'
import TabControl from 'components/content/tabControl/TabControl.vue'
import GoodsList from 'components/content/goods/GoodsList.vue'
import BackTop from 'components/content/backTop/BackTop.vue';

import HomeSwiper from './childComponents/HomeSwiper.vue'
import HomeRecommendView from './childComponents/HomeRecommendView.vue'
import FeatureView from './childComponents/FeatureView.vue'

import {getHomeMultidata,getHomeGoods} from "network/home.js"
import {debounce} from 'components/common/utils.js'

export default {
    name:"home",
    components:{
      TabControl,
      NavBar,
      GoodsList,
      HomeSwiper,
      HomeRecommendView,
      FeatureView,
      Scroll,
      BackTop
    },
    data(){
      return{
        banners:[],
        recommends:[],
        goods:{
          'new':{page:0,list:[]},
          'pop':{page:0,list:[]},
          'sell':{page:0,list:[]}
        },
        currentType:'new',
        isShowBackTop:false
      }
    },
    created(){
        // 1.请求多个数据
        this.getHomeMultidata(),
        // 2.请求商品数据
        this.getHomeGoods('new')
        this.getHomeGoods('pop')
        this.getHomeGoods('sell')
    },
    mounted(){
          const refresh=debounce(this.$refs.scroll.refresh,500)
          this.$bus.$on("itemImageLoad",()=>{
            refresh()
        })
    },

    computed:{
      showGoods(){
        return this.goods[this.currentType].list
      }
    },
    methods:{
      //事件监听相关方法
      tabClick(index){
        switch(index){
          case 0:
            this.currentType='new'
            break
          case 1:
            this.currentType='pop'
            break
          case 2:
            this.currentType='sell'
            break
            //case有穿透
        }
      },
      backClick(){
        this.$refs.scroll.scrollTo(0,0)  //500ms回到顶部
      },
      contentScroll(position){
        this.isShowBackTop = (-position.y)>1000
      },
      loadMore(){
        // console.log('加载更多');
        this.getHomeGoods(this.currentType)
      },
      //网络请求相关方法
      getHomeMultidata(){
        getHomeMultidata().then(res=>{
        // this.result=res
        this.banners=res.data.banner.list
        this.recommends=res.data.recommend.list
      })
      },
      getHomeGoods(type){
        const page=this.goods[type].page+1
        getHomeGoods(type,page).then(res=>{
          this.goods[type].list.push(...res.data.list)
          this.goods[type].page+=1

          //完成上拉加载更多
          this.$refs.scroll.finishPullUp()
      })
      }
    }
}

</script>

<style scoped>
  #home{
    padding-top: 44px;
    position: relative;
    height: 100vh;
  }
  .home-nav{
    background-color: var(--color-tint);
    color: #fff;
    position: fixed;
    left: 0;
    right: 0;
    top:0;
    z-index: 9;
  }
  .tab-control{
    position: sticky;
    top: 44px;
    background-color: #fff;
    z-index: 9;
  }
  .content{
      background-color: #fff;
      overflow: hidden;

      position: absolute;
      top: 44px;
      bottom: 49px;
      left: 0;
      right: 0;
  }
  .top{
    height: 44px;
    position: sticky;
    bottom: 60px;
    right: 10px;
  }
</style>