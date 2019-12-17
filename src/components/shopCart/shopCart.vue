<template>
    <div>
        <div class="shopcart">
            <div class="content" @click="toggleList">
                <div class="content-left">
                    <div class="logo-wrapper">
                        <div class="logo" :class="{'highlight': totalCount>0}">
                            <i class="icon-shopping_cart" :class="{'highlight':totalCount>0}"></i>
                        </div>
                        <div class="num" v-if="totalCount > 0">{{ totalCount }}</div>
                    </div>
                    <div class="price" :class="{'highlight': totalPrice>0}">￥{{ totalPrice }}</div>
                    <div class="delivery">另需配送费￥{{ deliveryPrice }}元</div>
                </div>
                <div class="content-right">
                    <div class="pay" :class="payClass">{{ pay }}</div>
                </div>
            </div>
            <div class="balls-wrapper">
                <div v-for="ball in balls">
                    <transition
                            name="drop"
                            @before-enter="beforeDrop"
                            @enter="dropping"
                            @after-enter="afterDrop"
                    >
                        <div class="ball" v-show="ball.show">
                            <div class="ball-inner"></div>
                        </div>
                    </transition>
                </div>
            </div>
            <transition name="fold">
                <div class="cart-detail" v-if="showList">
                    <div class="cart-detail-header">
                        <h1 class="title">购物车</h1>
                        <div class="empty" @click="empty">清空</div>
                    </div>
                    <div class="cart-detail-content" ref="cartDetail">
                        <ul>
                            <li class="cart-detail-item border-1px" v-for="food in selectFoods">
                                <div class="cart-detail-food">
                                    <p class="name">{{ food.name }}</p>
                                    <span class="price">￥{{ food.price }}</span>
                                </div>
                                <div class="cart-control-wrapper">
                                    <cartcontrol @addFood="addFood" :food="food" />
                                </div>
                            </li>
                        </ul>
                    </div>
                </div>
            </transition>
        </div>
        <!-- 遮罩层 -->
        <transition name="fade">
            <div class="mask" v-if="showList" @click="hideList"></div>
        </transition>
    </div>
</template>
<script>
    import BScroll from "better-scroll";
    import cartcontrol from "../cartControl/Control.vue";
    export default {
        props: {
            selectFoods: {
                type: Array,
                default() {
                    return [];
                }
            },
            deliveryPrice: {
                type: Number,
                // default:0,
                require: true
            },
            minPrice: {
                type: Number,
                // default:0,
                require: true
            }
        },
        data() {
            return {
                balls: [
                    { show: false },
                    { show: false },
                    { show: false },
                    { show: false },
                    { show: false },
                    { show: false }
                ],
                dropBalls: [], //下落的小球
                fold: true
            };
        },
        computed: {
            //计算价格  总价
            totalPrice() {
                let sumPrice = 0;
                this.selectFoods.forEach(item => {
                    item.count && (sumPrice += item.price * item.count);
                });
                return sumPrice;
            },
            //计算总数  数量
            totalCount() {
                let sumCount = 0;
                this.selectFoods.forEach(item => {
                    item.count && (sumCount += item.count);
                });
                return sumCount;
            },
            pay() {
                if (this.totalPrice === 0) {
                    return `￥${this.minPrice}元起送`;
                } else if (this.totalPrice < this.minPrice) {
                    let diff = this.minPrice - this.totalPrice;
                    return `还差${diff}起送`;
                } else {
                    return `结算`;
                }
            },
            payClass() {
                if (this.totalPrice < this.minPrice) {
                    return "not-high";
                } else {
                    return "high";
                }
            },
            showList() {
                //当有商品加入购物车时，购物车详情面板才展示
                if (!this.totalCount) {
                    this.fold = true; //折叠
                    return false;
                }
                let show = !this.fold;
                if (show) {
                    this.$nextTick(() => {
                        if (!this.scroll) {
                            this.scroll = new BScroll(this.$refs.cartDetail, {
                                click: true
                            });
                        } else {
                            this.scroll.refresh(); //当DOM发生变化时，需要重新计算滚动高度;
                        }
                    });
                }
                return show;
            }
        },
        methods: {
            drop(el) {
                console.log(el);
                for (let i = 0; i < this.balls.length; i++) {
                    let ball = this.balls[i];
                    if (!ball.show) {
                        ball.show = true;
                        ball.el = el;
                        this.dropBalls.push(ball);
                        return;
                    }
                }
            },

            beforeDrop(el) {
                //初始化小球的位置
                let count = this.balls.length;
                while (count--) {
                    let ball = this.balls[count];
                    if (ball.show) {
                        //执行动画的那个小球
                        let rect = ball.el.getBoundingClientRect();
                        let x = rect.left - 40; //x轴坐标
                        let y = -(window.innerHeight - rect.top - 22); //y轴坐标
                        el.style.display = "";
                        el.style.webkitTransform = `translate3d(${x}px, 0, 0)`;
                        el.style.transform = `translate3d(${x}px, 0, 0)`;
                        let inner = el.querySelector(".ball-inner");
                        inner.style.webkitTransform = `translate3d(0, ${y}px, 0)`;
                        inner.style.transform = `translate3d(0, ${y}px, 0)`;
                    }
                }
            },
            dropping(el, done) {
                el.offsetTop; //保证小球的位置要时时更新
                this.$nextTick(() => {
                    el.style.webkitTransform = "translate3d(0, 0, 0)";
                    el.style.transform = "translate3d(0, 0, 0)";
                    let inner = el.querySelector(".ball-inner");
                    inner.style.webkitTransform = "translate3d(0, 0, 0)";
                    inner.style.transform = "translate3d(0, 0, 0)";
                    el.addEventListener("transitionend", done);
                });
            },
            afterDrop(el) {
                //回收小球
                let ball = this.dropBalls.shift();
                if (ball.show) {
                    el.style.display = "none";
                    ball.show = false;
                }
            },
            toggleList() {
                if (!this.totalCount) {
                    return;
                }
                this.fold = !this.fold;
            },
            hideList() {
                this.fold = true;
            },
            empty() {
                //清空
                this.selectFoods.forEach(food => {
                    food.count = 0;
                });
            },

            addFood(el) {
                this.drop(el);
            }
        },
        components: {
            cartcontrol
        }
    };
</script>
<style lang="scss" scoped>
    @import "../../common/mixin.scss";
    .shopcart {
        position: fixed;
        left: 0;
        bottom: 0;
        z-index: 50;
        width: 100%;
        height: 0.48rem;
        .content {
            display: flex;
            background: #141d27;
            font-size: 0;
            color: rgba(255, 255, 255, 0.4);
            .content-left {
                flex: 1;
                font-size: 0;
                .logo-wrapper {
                    display: inline-block;
                    // vertical-align: top;
                    position: relative;
                    top: -0.1rem;
                    margin: 0 0.24rem;
                    padding: 0.06rem;
                    width: 0.56rem;
                    height: 0.56rem;
                    border-radius: 50%;
                    background-color: #141d27;
                    .logo {
                        // vertical-align: top;
                        width: 100%;
                        height: 100%;
                        border-radius: 50%;
                        text-align: center;
                        background-color: #2b343c;
                        &.highlight {
                            background-color: rgb(0, 160, 220);
                        }
                        .icon-shopping_cart {
                            line-height: 0.48rem;
                            font-size: 0.24rem;
                            // color: #80858a;
                            &.highlight {
                                color: #fff;
                            }
                        }
                    }
                    .num {
                        position: absolute;
                        top: 0;
                        right: 0;
                        width: 0.24rem;
                        height: 0.16rem;
                        line-height: 0.16rem;
                        text-align: center;
                        border-radius: 0.06rem;
                        font-size: 0.09rem;
                        font-weight: 700;
                        color: #ffffff;
                        background-color: rgb(240, 20, 20);
                        box-shadow: 0 0.02rem 0.04rem 0 rgba(0, 0, 0, 0.4);
                    }
                }
                .price {
                    display: inline-block;
                    vertical-align: top;
                    max-width: 0.6rem;
                    margin-top: 0.12rem;
                    line-height: 0.24rem;
                    padding-right: 0.12rem;
                    font-size: 0.16rem;
                    font-weight: 700;
                    border-right: 1px solid rgba(255, 255, 255, 0.1);
                    &.highlight {
                        color: #fff;
                    }
                }
                .delivery {
                    display: inline-block;
                    vertical-align: top;
                    font-size: 0.12rem;
                    padding-left: 0.12rem;
                    // margin: 0.12rem 0 0 0.12rem;
                    line-height: 0.48rem;
                }
            }
            .content-right {
                width: 1.05rem;
                flex: 0 0 1.05rem;
                .pay {
                    height: 0.48rem;
                    line-height: 0.48rem;
                    text-align: center;
                    background: #2b333b;
                    font-size: 0.12rem;
                    font-weight: 700;
                    &.not-high {
                        background-color: #2b333b;
                    }
                    &.high {
                        background: rgb(0, 160, 220);
                        color: #fff;
                    }
                }
            }
        }
        .balls-wrapper {
            .ball {
                position: absolute;
                left: 0.4rem;
                bottom: 0.22rem;
                transition: all 0.4s linear;
                .ball-inner {
                    width: 0.16rem;
                    height: 0.16rem;
                    background-color: rgb(0, 160, 220);
                    border-radius: 50%;
                    transition: all 0.4s cubic-bezier(0.49, -0.06, 0.75, 0.14);
                }
            }
        }
        .cart-detail {
            position: absolute;
            top: 0;
            left: 0;
            z-index: -1;
            width: 100%;
            transform: translate3d(0, -100%, 0);
            &.fold-enter-active,
            &.fold-leave-active {
                transition: all 0.4s linear;
            }
            &.fold-enter,
            &.fold-leave-to {
                transform: translate3d(0, 0, 0);
            }
            .cart-detail-header {
                display: flex;
                padding: 0 0.18rem;
                height: 0.4rem;
                border: 1px solid rgba(7, 17, 27, 0.1);
                background-color: #f3f5f7;
                justify-content: space-between;
                align-items: center;
                .title {
                    font-size: 0.14rem;
                    font-weight: 200;
                    color: rgb(7, 17, 27);
                }
                .empty {
                    font-size: 0.12rem;
                    color: rgb(0, 160, 220);
                }
            }
            .cart-detail-content {
                max-height: 2.17rem;
                padding: 0 0.18rem;
                background-color: #fff;
                overflow: hidden;
                .cart-detail-item {
                    height: 0.48rem;
                    line-height: 0.48rem;
                    font-size: 0;
                    @include border-1px(rgba(7, 17, 27, 0.1));
                    .cart-detail-food {
                        position: relative;
                        display: flex;
                        margin-right: 0.12rem;
                        .name {
                            display: inline-block;
                            font-size: 0.14rem;
                            color: rgb(7, 17, 27);
                        }
                        .price {
                            position: absolute;
                            right: 0.7rem;
                            display: inline-block;
                            font-size: 0.14rem;
                            font-weight: 700;
                            color: rgb(240, 20, 20);
                        }
                    }
                    .cart-control-wrapper {
                        position: absolute;
                        right: 0;
                        top: 0.06rem;
                    }
                }
            }
        }
    }
    .mask {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        z-index: 40;
        opacity: 1;
        background-color: rgba(7, 17, 27, 0.6);
        backdrop-filter: blur(10px); //ios
        &.fade-enter-active,
        &.fade-leave-active {
            transition: all 0.4s;
        }
        &.fade-enter,
        &.fade-leave-to {
            opacity: 0;
            background-color: rgb(7, 17, 27);
        }
    }
</style>