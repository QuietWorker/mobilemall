<template>
  <div class="goods">
    <goods-list-item v-for="item in goods" :goodsItem="item">

    </goods-list-item>
  </div>
</template>

<script>
import GoodsListItem from './GoodsListItem.vue'
export default {
    name:"GoodsList",
    props:{
        goods:{
            type:Array,
            default(){
                return[]
            }
        }
    },
    components:{
        GoodsListItem
    }
    
}
</script>

<style scoped>
    .goods {
        display: flex;
        flex-wrap: wrap;
        justify-content: space-around;
        background-color: #eee;
        padding: 10px 0;
    }


</style>