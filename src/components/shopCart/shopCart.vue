<template>
    <div class="shop-cart">
        <div class="content">
            <div class="content-left">
                <div class="logo-wrapper">
                    <div class="logo" :class="{highlight : totalCount > 0}">
                        <i class="icon-shopping_cart" :class="{highlight : totalCount > 0}"></i>
                    </div>
                    <div class="num" v-if="totalCount > 0">{{ totalCount }}</div>
                </div>
                <div class="price" :class="{highlight : totalPrice > 0}">￥{{ totalPrice }}</div>
                <div class="delivery">另需配送费￥{{ deliveryPrice }}元</div>
            </div>
            <div class="content-right" :class="payClass">{{ pay }}</div>
        </div>
        <div class="balls-wrapper">
            <div class="ball" v-for="ball in balls">
                <div class="ball-inner"></div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        props : {
            selectFoods : {
                type : Array,
                default() {
                    return []
                }
            },
            deliveryPrice : {
                type : Number,
                required : true
            },
            minPrice : {
                type : Number,
                required : true
            },
        },
        data () {
          return {
              balls : [
                  { show : true },
                  { show : true },
                  { show : true },
                  { show : true },
                  { show : true },
                  { show : true }
              ]
          }
        },
        computed : {
            totalPrice () {//计算总价
                let sumPrice = 0;
                this.selectFoods.forEach(item => {
                    item.count && (sumPrice += item.price * item.count)
                });
                return sumPrice
            },
            totalCount () {//计算数量
                let sumCount = 0;
                this.selectFoods.forEach(item => {
                    item.count && (sumCount += item.count)
                });
                return sumCount
            },
            pay () {
                if (this.totalPrice === 0) {
                    return `${this.minPrice}元起送`
                }else if (this.totalPrice < this.minPrice) {
                    let diff = this.minPrice - this.totalPrice;
                    return `还差${diff}元起送`
                }else {
                    return `去结算`
                }
            },
            payClass () {
                if (this.totalPrice < this.minPrice) {
                    return `not-high`;
                }else {
                    return `high`;
                }
            }
        }

    }
</script>

<style lang="scss" scoped>
.shop-cart {
    position: fixed;
    left: 0;
    bottom: 0;
    width: 100%;
    height: .48rem;
    .content {
        display: flex;
        background-color: #141d27;
        color: rgba(255,255,255,.4);
        .content-left {
            font-size: 0;
            flex: 1;
            .logo-wrapper {
                display: inline-block;
                position: relative;
                top: -.1rem;
                margin: 0 .24rem;
                padding: .06rem;
                width: .56rem;
                height: .56rem;
                z-index: 50;
                border-radius: 50%;
                background-color:  #141d27;
                .logo {
                    width: 100%;
                    height: 100%;
                    border-radius: 50%;
                    background-color: #2b343c;
                    text-align: center;
                    &.highlight {
                        background-color: rgb(0,160,220);
                    }
                    .icon-shopping_cart {
                        line-height: .44rem;
                        font-size: .24rem;
                        &.highlight {
                            color: #fff;
                        }
                    }
                }
                .num {
                    position: absolute;
                    right: 0;
                    top: 0;
                    width: .24rem;
                    height: .16rem;
                    line-height: .16rem;
                    border-radius: .06rem;
                    background-color: rgb(240,20,20);
                    box-shadow: 0 .02rem .04rem 0 rgba(0,0,0,.4);
                    font-size: .09rem;
                    font-weight: 700;
                    color: #ffffff;
                    text-align: center;
                }
            }
        }
        .price {
            display: inline-block;
            vertical-align: top;
            margin-top: .12rem;
            padding-right: .12rem;
            border-right: 1px solid rgba(255,255,255,.1);
            font-size: .16rem;
            font-weight: 700;
            line-height: .24rem;
            &.highlight {
                color: #fff;
            }
        }
        .delivery {
            display: inline-block;
            font-size: .12rem;
            line-height: .48rem;
            vertical-align: top;
            padding-left: .12rem;
        }
        .content-right {
            flex: 0 0 1.05rem;
            width: 1.05rem;
            line-height: .48rem;
            font-size: .12rem;
            font-weight: 700;
            text-align: center;
            &.not-high {
                background-color: #2b333b;
            }
            &.high {
                background-color: rgb(0,160,220);
                color: #fff;
            }
        }
    }
    .balls-wrapper {
        .ball {
            position: absolute;
            left: .32rem;
            top: .22rem;
            .ball-inner {
                background-color:red;
                width: .16rem;
                height: .16rem;
                border-radius : 50%;
            }
        }
    }
}
</style>