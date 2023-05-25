<template>
    <div class="details-nav-bar">
        
    <nav-bar>
        <div slot="left" @click="backClick">
            <img src="~assets/img/common/back.svg" alt="" class="back" >
        </div>
        <div slot="center" class="title">
            <div v-for="(item,index) in titles" :key="item.id" 
                class="title-item"
                :class="{active:index===currentIndex}"
                @click="titleClick(index)"
                >{{item}}</div>
        </div>
    </nav-bar>
    </div>
 
</template>

<script>
import NavBar from "components/common/navbar/NavBar.vue"
export default {
    name:'DetailsNavBar',
    components:{
        NavBar
    },
    props:{
        Index:{
            type:Number,
            default(){
                return 0;
            }
        }
    },
    data(){
        return{
            titles:['商品','详情','参数','评论'],
            currentIndex: 0
        }
    },
    methods:{
        titleClick(index){
            this.currentIndex=index;
            this.$emit("themeClick", this.currentIndex);
        },
        backClick(){
            this.$router.back()
        }
    },
    watch: {
        Index() {
            this.currentIndex = this.Index;
        },
    },

}
</script>

<style scoped>
    .title{
        display: flex;
    }
    .title-item{
        flex: 1;
        font-size: 15px;
    }
    .back{
        margin-top: 12px;
    }
    .active{
        color: var(--color-high-text);
    }
</style>