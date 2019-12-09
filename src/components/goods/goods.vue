<template>
    <div class="goods">
        <div class="nav-menu" ref="menuWrapper">
            <ul>
                <li class="menu-item"
                    :class="{ active : currentIndex === index }"
                    v-for="(item,index) in goods"
                    :key="index"
                    @click="selectMenu(index)"
                >
                    <span class="text border-1px">
                        <span class="icon" :class="classMap[item.type]" v-if="item.type > 0"></span>
                        {{ item.name }}
                    </span>
                </li>
            </ul>
        </div>
        <div class="foods-wrapper" ref="foodsWrapper">
            <ul>
                <li class="food-list" v-for="item,index in goods" :key="index" ref="foodList">
                    <h1 class="title">{{ item.name }}</h1>
                    <ul>
                        <li class="food-item border-1px" v-for="food,idx in item.foods" :key="idx">
                            <div class="icon">
                                <img width="57" height="57" :src="food.icon" alt="">
                            </div>
                            <div class="content">
                                <h2 class="name">{{ food.name }}</h2>
                                <div class="desc">{{ food.description }}</div>
                                <div class="extra">
                                    <span class="count">月售{{ food.sellCount }}份</span>
                                    <span>{{ food.rating }}%</span>
                                </div>
                                <div class="price">
                                    <span class="new-price">￥{{ food.price }}</span>
                                    <span class="old-price" v-if="food.oldPrice">￥{{ food.oldPrice }}</span>
                                </div>
                            </div>
                        </li>
                    </ul>
                </li>
            </ul>
        </div>
        <shop-cart v-if="seller.deliveryPrice" :deliveryPrice="seller.deliveryPrice" :minPrice="seller.minPrice" :selectFoods="selectFoods"/>
    </div>
</template>
<script>
    import BScroll from 'better-scroll';
    import getData from '@/api/header';
    import ShopCart from '@/components/shopCart/shopCart.vue'
    export default {
        props : {
            seller : {
                type : Object
            }
        },
        data () {
            return {
                goods : [],
                listHeight : [],//用于存储每一块(foods-item)的高度
                scrollY : 0
            }
        },
        computed : {
            selectFoods () {
                let foods = [];
                this.goods.forEach(good => {
                    good.foods.forEach(food => {
                        foods.push(food)
                    })
                });
                return foods;
            },
            currentIndex () {//计算index
                for (let i = 0; i < this.listHeight.length; i++){
                    let height1 = this.listHeight[i];
                    let height2 = this.listHeight[i+1];
                    if (!height2 || this.scrollY >= height1 && this.scrollY < height2){
                        return i;//i 就是要找的index
                    }
                }
            }
        },
        async created() {
            this.classMap = ['decrease','discount','guarantee','invoice','special']
            this.goods = await getData('/api/goods');
            await this.$nextTick()//当DOM更新完毕后执行的函数
            // this.$nextTick(() => {
            //     this.initScroll()
            // })
            this.initScroll();
            this.computedHeight()
        },
        methods : {
            initScroll () {
                //左侧导航的scroll
              this.menuScroll = new BScroll(this.$refs.menuWrapper,{
                  click : true//让点击事件生效
              });
              //右侧列表的scroll
              this.foodsScroll = new BScroll(this.$refs.foodsWrapper,{
                  click : true,
                  probeType : 3//实时监听scroll事件，包括触底反弹动画
              });
              //监听滚动事件
                this.foodsScroll.on('scroll',pos => {
                   this.scrollY = Math.abs(pos.y)
                })
            },
            computedHeight () {//计算高度
                let foodList = this.$refs.foodList;
                let height = 0;
                this.listHeight.push(height);
                foodList.forEach(item => {
                    height += item.clientHeight;
                    this.listHeight.push(height)
                })
            },
            selectMenu (idx) {
                let foodsList = this.$refs.foodList;
                let foodList = foodsList[idx];
                this.foodsScroll.scrollToElement(foodList,500)
            }
        },
        components : {
            ShopCart
        }
    }
</script>

<style lang="scss" scoped>
    @import "../../common/mixin";
.goods {
    display: flex;
    width: 100%;
    position: absolute;
    top: 1.74rem;
    bottom: .48rem;
    overflow: hidden;
    .nav-menu {
        flex: 0 0 .8rem;
        width: .8rem;
        .menu-item {
            width: 100%;
            display: table;
            height: .54rem;
            padding: 0 .12rem;
            background: #f3f5f7;
            &.active {
                background: #fff;
                .text {
                    font-weight: 700;
                }
            }
            &:last-child {
                .text {
                    @include border-none
                }
            }
            .text {
                display: table-cell;
                vertical-align: middle;
                line-height: .16rem;
                font-size: .12rem;
                font-weight: 200;
                @include border-1px(rgba(7,17,27,.1));
            }
            .icon {
                display: inline-block;
                vertical-align: middle;
                width: .12rem;
                height: .12rem;
                margin-right: .02rem;
                &.decrease {
                    @include bg-image('./img/decrease_3')
                }
                &.discount {
                    @include bg-image('./img/discount_3')
                }
                &.guarantee {
                    @include bg-image('./img/guarantee_3')
                }
                &.invoice {
                    @include bg-image('./img/invoice_3')
                }
                &.special {
                    @include bg-image('./img/special_3')
                }
            }
        }
    }
    .foods-wrapper {
        flex: 1;
        .title {
            padding-left: .14rem;
            height: .26rem;
            background-color: #f3f5f7;
            line-height: .26rem;
            font-size: .12rem;
            color: rgb(147,153,159);
            border-left: 2px solid #d9dde1;
        }
        .food-item {
            display: flex;
            margin: .18rem .18rem 0;
            padding-bottom: .14rem;
            @include border-1px(rgba(7,17,27,.1));
            &:last-child {
                @include border-none
            }
            .icon {
                flex: 0 0 .57rem;
                width: .57rem;
                height: .57rem;
                margin-right: .1rem;
                img {
                    vertical-align: top;
                }
            }
            .content {
                flex: 1;
                margin-top: .02rem;
                .name {
                    font-size: .14rem;
                    color: rgb(7,17,27);
                    line-height: .14rem;
                }
                .desc,.extra {
                    font-size: .1rem;
                    line-height: .1rem;
                    color: rgb(147,153,159);
                }
                .desc {
                    margin: .08rem 0;
                    line-height: .14rem
                }
                .extra {
                    .count {
                        margin-right: .12rem;
                    }
                }
                .price {
                    margin-top: .04rem;
                    line-height: .24rem;
                    font-weight: 700;
                    font-size: 0;
                    .new-price {
                        font-size: .14rem;
                        color: rgb(240,20,20);
                    }
                    .old-price {
                        margin-left: .08rem;
                        font-size: .1rem;
                        color: rgb(147,153,159);
                        text-decoration: line-through;
                    }
                }
            }
        }
    }
}
</style>