<template>
    <transition name="move">
        <div class="food-detail" v-if="showFlag">
           <div class="food-content">
               <div class="food-detail-header">
                   <div class="header-img">
                       <img :src="food.image" alt="">
                        <div class="back" @click="showFlag = false">
                            <i class="icon-arrow_lift"></i>
                        </div>
                   </div>
                   <div class="header-content">
                       <h1 class="name">{{ food.name }}</h1>
                       <div class="extra">
                           <span class="count">月售{{ food.sellCount }}份</span>
                           <span class="rating">好评率{{ food.rating }}</span>
                       </div>
                       <div class="price">
                           <div class="news-price">{{ food.price }}</div>
                           <div class="old-price" v-if="food.oldPrice">{{ food.oldPrice }}</div>
                       </div>
                       <div class="buy" v-if="!food.count || food.count === 0">加入购物车</div>
                       <div class="cart-control">
                           <CartControl :food="food" />
                       </div>
                   </div>
               </div>
           </div>
        </div>
    </transition>
</template>

<script>
    import CartControl from '@/components/cartControl/Control.vue'
    export default {
        props : {
            food : {
                type : Object
            }
        },
        data () {
            return {
                showFlag : false
            }
        },
        methods : {
            show () {
                this.showFlag = true;
            }
        },
        components : {
            CartControl
        }
    }
</script>

<style lang="scss" scoped>
.food-detail {
    position: absolute;
    top: 0;
    left: 0;
    bottom: .48rem;
    width: 100%;
    height: 100%;
    z-index: 40;
    overflow: hidden;
    background-color: #ffffff;
    transform: translate3d(0,0,0);
    &.move-enter-active,&.move-leave-active {
        transition : .3s linear;
    }
    &.move-enter,&.move-leave-to {
        transform: translate3d(100%,0,0);
    }
    .food-content {
        font-size: 0;
        .food-detail-header {
            .header-img {
                position: absolute;
                top: 0;
                left: 0;
                width: 100%;
                height: 0;
                padding-top: 100%;
                img {
                    width: 100%;
                    height: 100%;
                    position: absolute;
                    top: 0;
                    left: 0;
                }
                .back {
                    padding: .1rem;
                    position: absolute;
                    left: 0;
                    top: 0;
                    .icon-arrow_lift {
                        font-size: .2rem;
                        font-weight: 700;
                        color: #ffffff;
                    }
                }
            }
        }
    }
}
</style>