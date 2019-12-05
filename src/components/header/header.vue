<template>
    <div class="header">
        <div class="content-wrapper">
            <div class="avatar">
                <img :src="seller.avatar" alt="">
            </div>
            <div class="content">
                <div class="title">
                    <span class="brand"></span>
                    <span class="name">{{ seller.name }}</span>
                </div>
                <div class="description">{{ seller.description }}/{{ seller.deliveryTime  }}分钟送达</div>
                <div class="support" v-if="seller.supports">
                    <span class="icon" :class="classMap[seller.supports[0].type]"></span>
                    <span class="text">{{ seller.supports[0].description }}</span>
                </div>
            </div>
            <div class="support-count" v-if="seller.supports">
               <!--<i class="icon-keyboard_arrow_right"></i>-->
                <span class="count">{{ seller.supports.length }}个</span>
                <i class="icon-keyboard_arrow_right"></i>
            </div>
        </div>
        <div class="bulletin-wrapper">
            <span class="bulletin-title"></span>
            <span class="bulletin-text">{{ seller.bulletin }}</span>
            <i class="icon-keyboard_arrow_right"></i>
        </div>
        <div class="background">
            <img :src="seller.avatar" alt="">
        </div>
        <div class="detail">
            <div class="detail-wrapper clearfix">
                <div class="detail-main" >
                    <div class="detail-title">{{ seller.name }}</div>
                </div>
            </div>
            <div class="detail-close">
                <i class="icon-close"></i>
            </div>
        </div>
    </div>
</template>
<script>
    export default {
        props : {
            seller : {
                type : Object,
                required : true
            }
        },
        created() {
            this.classMap = ['decrease','discount','guarantee','invoice','special']
        }
    }
</script>
<style lang="scss" scoped>
    @import "../../common/mixin";
.header {
    position: relative;
    color: rgb(255,255,255);
    background: rgba(7,17,27,.5);
    overflow: hidden;
    font-size: 0;
    .content-wrapper {
        position: relative;
        padding: .24rem .12rem .18rem .24rem;
        .avatar {
            display: inline-block;
            width: .64rem;
            height: .64rem;
            img {
                vertical-align: top;//设置文字相对于基线的对齐方式
                width: 100%;
                height: 100%;
                border-radius: 2px;
            }
        }
        .content {
            display: inline-block;
            margin: .02rem 0 .02rem .16rem;
            vertical-align: top;
            font-size: 0;
            .title {
                margin-bottom: .08rem;
                .brand {
                    display: inline-block;
                    vertical-align: top;
                    margin-right: .06rem;
                    width: .3rem;
                    height: .18rem;
                    @include bg-image('./img/brand');
                    background-size: 100% 100%;
                }
                .name {
                    vertical-align: top;
                    font-size: .16rem;
                    font-weight: bold;
                    line-height: .18rem;
                }
            }
            .description {
                margin-bottom: .1rem;
                line-height: .12rem;
                font-size: .12rem;
                font-weight: 200;
            }
            .support {
                .icon {
                    display: inline-block;
                    vertical-align: top;
                    margin-right: .04rem;
                    width: .12rem;
                    height: .12rem;
                    &.decrease {
                        @include bg-image('./img/decrease_1')
                    }
                    &.discount {
                        @include bg-image('./img/discount_1')
                    }
                    &.guarantee {
                        @include bg-image('./img/guarantee_1')
                    }
                    &.invoice {
                        @include bg-image('./img/invoice_1')
                    }
                    &.special {
                        @include bg-image('./img/special_1')
                    }
                }
                .text {
                    font-weight: 200;
                    font-size: .1rem;
                    line-height: .12rem;
                }
            }
        }
        .support-count {
            display: flex;
            padding: 0 .08rem;
            align-items: center;
            position: absolute;
            right: .12rem;
            bottom: .14rem;
            height: .24rem;
            font-size: 0;
            border-radius: .14rem;
            background-color: rgba(0,0,0,.2);
            .count {
                line-height: .12rem;
                font-weight: 200;
                font-size: .1rem;
            }
            .icon-keyboard_arrow_right {
                font-size: .1rem;
                font-weight: 200;
            }
        }
    }
    .bulletin-wrapper {
        display: flex;
        align-items: center;
        height: .28rem;
        padding: 0 .12rem;
        background: rgba(7,17,27,0.2);
        .bulletin-title {
            width: .22rem;
            height: .12rem;
            @include bg-image('./img/bulletin')
        }
        .bulletin-text {
            width: 90%;
            margin: 0 .04rem;
            font-size: .1rem;
            font-weight: 200;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
        }
        .icon-keyboard_arrow_right {
            font-size: .1rem;
        }
    }
    .background {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 1.34rem;
        filter : blur(10px);
        z-index: -1;
        img{
            width: 100%;
            height: 100%;
        }
    }
    .detail {
        position: fixed;
        top: 0;
        left: 0;
        bottom: 0;
        right: 0;
        height: 100%;
        width: 100%;
        overflow: auto;
        z-index: 100;
        background: rgba(7,17,27,0.8);
        .detail-wrapper {
            min-height: 100%;
            .detail-main {
                margin-top: .64rem;
                padding-bottom: .64rem;
                font-size: 20px;
                .detail-title {
                    text-align: center;
                    line-height: .16rem;
                    font-size: .16rem;
                    font-weight: 700;
                }
            }
        }
        .detail-close {
            position: relative;
            margin: -.64rem auto 0 auto;
            font-size: .32rem;
            width: .32rem;
            height: .32rem;
            color: rgba(255,255,255,.5);
            clear: both;
        }
    }
}
</style>