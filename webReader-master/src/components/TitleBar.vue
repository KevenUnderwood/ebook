<template>
    <transition name="slide-down">
        <div class="title-wrapper" v-show="ifTitleAndMenuShow">
            <div class="left">
                <span class="icon-circle-left icon"></span>
            </div>
            <div class="right">
                <div class="icon-wrapper">
                    <span class="icon-cart icon"></span>
                </div>
                <div class="icon-wrapper">
                    <span class="icon-user icon"></span>
                </div>
                <div class="icon-wrapper">
                    <span class="icon-menu icon "></span>
                </div>
            </div>
        </div>
        </transition>
</template>

<script>
    export default {
        props: {
            ifTitleAndMenuShow: {
                type: Boolean,
                default: false
                }
        }
    }
</script>

<style lang='scss' scoped>
    @import '../assets/styles/global';
.title-wrapper {
            position: absolute;
            top: 0;
            left: 0;
            z-index: 101;
            display: flex;
            width: 100%;
            height: px2rem(48);
            background-color: #fff;
            box-shadow: 0 px2rem(8) px2rem(8) rgba($color: #000000, $alpha: .15);
            .left {
                flex: 0 0 px2rem(60);
                @include center;
            }
            .right {
                flex: 1;
                display: flex;
                justify-content: flex-end;
                .icon-wrapper {
                    flex: 0 0 px2rem(40);
                    @include center; 
                }
            }
        }
</style>