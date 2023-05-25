<template>
  <div class="details-swiper">
    <swiper>
    <swiper-item v-for="item in topImages" :key="item.id"  class="al">
        <img :src="item" alt="">
    </swiper-item>
  </swiper>
  </div>
</template>

<script>
import {Swiper,SwiperItem} from 'components/common/swiper/index.js'
export default {
    name:"DetailsSwiper",
    components:{
        Swiper,
        SwiperItem
    },

    props:{
        topImages:{
            type:Array,
            default(){
                return []
            }
        }
    }
}
</script>

<style scoped>
    .al{
        height: 300px;
        overflow: hidden;
    }
</style>