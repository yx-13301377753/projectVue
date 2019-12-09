<template>
    <div class="star" :class="starType">
        <span
                v-for="classItem,index in starClasses"
                class="star-item"
                :class="classItem"
                :key = "index"
        ></span>
    </div>
</template>
<script>
    const LENGTH = 5;//用来控制星星的个数
    const CLS_ON = 'on';//on的class样式，全星;
    const CLS_HALF = 'half';//half的class样式，半星;
    const CLS_OFF = 'off';//off的class样式，没星;


    export default {
        props : {
            size : {
                type : Number,
                require : true
            },
            score : {
                type : Number,
                require: true
            }
        },
        computed : {
            starType () {//用来计算不同的星星尺寸的class名
                return 'star-' + this.size
            },
            starClasses () {//用来计算显示的是全星还是半星的class名
                let result = [];
                let score = Math.floor(this.score * 2) / 2;//星星的分数
                let floorNum = score % 1 !== 0; //小数
                let intiger = parseInt(score);
                for (let i = 0; i < intiger; i++){
                    result.push(CLS_ON);//整颗星
                }
                if(floorNum){
                    result.push(CLS_HALF);//半颗星
                }
                while (LENGTH > result.length){//没星
                    result.push(CLS_OFF)
                }
                return result
            }
        }
    }
</script>
<style lang="scss" scoped>
    @import "../../common/mixin";
.star {
    font-size: 0;
    .star-item {
        display: inline-block;
        &:last-child {
            margin-right: 0 !important;
        }
    }
    &.star-48 > .star-item {
        width: .2rem;
        height: .19rem;
        margin-right: .2rem;
        &.on {
            @include bg-image('./img/star48_on')
        }
        &.half {
            @include bg-image('./img/star48_half')
        }
        &.off {
            @include bg-image('./img/star48_off')
        }
    }

    &.star-36 > .star-item {
        width: .15rem;
        height: .15rem;
        margin-right: .06rem;
        &.on {
            @include bg-image('./img/star36_on')
        }
        &.half {
            @include bg-image('./img/star36_half')
        }
        &.off {
            @include bg-image('./img/star36_off')
        }
    }

    &.star-24 > .star-item {
        width: .1rem;
        height: .1rem;
        margin-right: .03rem;
        &.on {
            @include bg-image('./img/star24_on')
        }
        &.half {
            @include bg-image('./img/star24_half')
        }
        &.off {
            @include bg-image('./img/star24_off')
        }
    }
}
</style>