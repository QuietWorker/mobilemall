<template>
  <div class="wrapper" ref="wrapper">
    <div class="content">
        <slot>

        </slot>
    </div>
  </div>
</template>

<script>
import BScroll from 'better-scroll'
export default {
    name:'Scroll',
    props:{
        probeType:{
            type:Number,
            default:0
        },
        pullUpLoad:{
            type:Boolean,
            default:true
        }
    },
    data(){
        return{
            scroll:null
        }
    },
    
    mounted(){
        // 1.创建BScroll对象
        //通过document.querySelector('wrapper')来获取wrapper不是特别好，因为如果有其他的标签设置wrapper,通过这个来获取的就不知道是哪一个了
        this.scroll=new BScroll(this.$refs.wrapper,{
            click:true,
            probeType:this.probeType,
            pullUpLoad:this.pullUpLoad
        })
        
        //2.监听滚动的位置
        this.scroll.on('scroll',(position)=>{
            // console.log(position);
            if(this.probeType==2||this.probeType==3){
                this.$emit('scroll',position)
            }
        })
        //3.监听上拉事件
        this.scroll.on('pullingUp',()=>{
            console.log('上拉加载更多');
            this.$emit('pullingUp')
        })
    },
    methods:{
        scrollTo(x,y,time=300){
            this.scroll&&this.scroll.scrollTo(x,y,time)
        },
        finishPullUp(){
            this.scroll&&this.scroll.finishPullUp()
        },
        refresh(){
            this.scroll&&this.scroll.refresh()
        },
        getScrollY(){
            return this.scroll ? this.scroll.y : 0
        }
    }
}
</script>

<style scoped>
    
</style>