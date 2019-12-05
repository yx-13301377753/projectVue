<template>
  <div id="app">
  <v-header :seller = "seller" />
   <div class="tab border-1px">
     <div class="tab-item">
       <router-link to="/goods">商品</router-link>
     </div>
     <div class="tab-item">
       <router-link to="/ratings">评价</router-link>
     </div>
     <div class="tab-item">
       <router-link to="/seller">商家</router-link>
     </div>
   </div>
    <router-view />
  </div>
</template>

<script>
import VHeader from '@/components/header/header.vue';
import getData from '@/api/header.js'
export default {
    data () {
        return {
            seller : {}
        }
    },
    async created (){
        this.seller = await getData('/api/seller');
    },
  components: {
    VHeader,
  },
}
</script>
<style lang="scss" scoped>
    @import "./common/mixin";
    .tab {
        display: flex;
        .tab-item {
            flex : 1;
            height: .4rem;
            line-height: .4rem;
            font-size: .14rem;
            text-align: center;
            color: rgb(77,85,93);
            @include border-1px(rgba(7,17,27,0.1));
            & > a {
                display: block;
                &.active {
                    color:rgb(240,20,20)
                }
            }
        }
    }
</style>


