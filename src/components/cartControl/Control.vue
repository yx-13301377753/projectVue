<template>
    <div class="cart-control">
        <transition name="move">
            <div class="decrease" @click.stop.prevent="food.count --" v-if="this.food.count > 0">
                <span class="icon-remove_circle_outline"></span>
            </div>
        </transition>
        <div class="count" v-if="this.food.count > 0">{{ food.count }}</div>
        <div class="add icon-add_circle" @click.stop.prevent="addCart"></div>
    </div>
</template>

<script>
    export default {
        props : {
          food : {
              type : Object
          }
        },
        data () {
            return {

            }
        },
        methods : {
            addCart (event) {
                if (!this.food.count){
                    this.$set(this.food,'count',1)
                }else {
                    this.food.count ++;
                }
                this.$emit('addFood',event.target,this.food)
            }
        }
    }
</script>

<style lang="scss" scoped>
    .cart-control {
        font-size: 0;
        .decrease {
            display: inline-block;
            vertical-align: top;
            padding: .06rem;
            transform: translate3d(0,0,0);
            opacity: 1;
            &.move-enter-active,&.move-leace-active {
                transition: all .4s linear;
            }
            &.move-enter,&.move-leave-to {
                transform: translate3d(.24rem,0,0);
                opacity: 0;
                .icon-remove_circle_outline {
                    transform: rotate(180deg);
                }
            }
            .icon-remove_circle_outline{
                display: inline-block;
                line-height: .24rem;
                font-size: .24rem;
                color: rgb(0,160,220);
                transform: rotate(0);
                transition: all .4s linear;
            }
        }
        .count {
            display: inline-block;
            font-size: .1rem;
            line-height: .36rem;
            color: rgb(147,153,159);
            vertical-align: top;
        }
        .add {
            display: inline-block;
            vertical-align: top;
            line-height: .24rem;
            font-size: .24rem;
            color: rgb(0,160,220);
            padding: .06rem;
        }
    }
</style>