<template>
    <div :class="[type !='home'? 'mb-5': '',' tab-item']">
        <div class="list-detail">
            <div class="banner">
                <slot name="swiperBanner"></slot>
                <img class="lazy" :src="tabBanner" alt="" v-if="this.type !='home'">
            </div>
        </div>

        <div class="sublist-item sublist-item-md cate-icon">
            <div class="list-primary" v-for="(cate, index) in cateList" :key="index" @click="jump(cate)">
                <span class="list-primary-logo">
                    <img class="lazy" v-lazy="cate.icon">
                </span>
                <span class="list-item-tit">{{cate.title}}</span>
            </div>
        </div>
    </div>
</template>
<script>
require('swiper/dist/css/swiper.css')
require('../../assets/js/sks-photo')
export default {
    data() {
        return {
            swiperOption: {
                notNextTick: true,
                autoplay: 3000,
                loop: true,
                pagination: '.swiper-pagination'
            },
            hasBanner: false,
            cateIcon: '',
            subscribeImg: {
                img: 'uploads/home/checkSubscribe.png'
            },
            // 是否显示轮播图
            isSwiper: false,
            showBanner: false
        }
    },
    props: ['cateList', 'tabBanner', 'type', 'isCheckSubscribe'],
    mounted() {
        // if (this.tabBanner != '' && this.type !='home') {
        //     console.log(this.tabBanner)
        //     this.hasBanner = true
        // }
        // 传过来的banner是数组否是数组

    },
    methods: {
        // 根据不同的类型跳转到不同的页面
        jump(cate) {
            if (this.type == 'home' && !cate.id) {
                this.$router.push(this.$route.path + '/goods-cate/index')
            } else {
                switch (this.type) {
                    case 'cateGoods':
                        this.$router.push(this.$route.path + '/goods/list/' + cate.rowId + '/' + cate.id)
                        break
                    case 'global':
                        this.$router.push(this.$route.path + '/goods/list/' + cate.id)
                        break
                    default:
                        this.$router.push(this.$route.path + '/recommend/' + cate.id)
                        break
                }
            }
        }
    }
}
</script>
<style scoped lang="scss">
.list-item-tit {
    font-size: 0.24rem !important;
}

.swiper-slide {
    a {
        display: block;
    }
}

</style>
