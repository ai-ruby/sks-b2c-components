<template>
    <!-- 首页显示 Start -->
    <mescroll class="cate-two swiper-slide stop-swiping" ref="homeGobal">
        <!-- banner + 分类栏 -->
        <cate :tabBanner="banner" :cateList="homeCateList" :type="'global'"></cate>

        <!-- 热销 横向滑动 -->
        <cross-scroll :hotList="hotList" :type="'global'" class="mb-5"></cross-scroll>

        <!-- 9个商品 -->
        <goods-list :itemList="goodsList" :type="'global'"></goods-list>

        <load-more :isShowLoadMore="showLoadMore"></load-more>
    </mescroll>
    <!-- 首页显示 End -->
</template>
<script>
import loadMore from '@/components/common/load-more'
import { getGlobalTab, getGlobalList } from '@/api/b2cHome'
import cate from '@/components/b2c/cate'
import crossScroll from '@/components/b2c/cross-scroll'
import goodsList from '@/components/b2c/goods-list'
export default {
    data() {
        return {
            banner: '',
            homeCateList: '',
            hotList: '',
            goodsList: '',
            mescroll: '',
            flag: true,
            showLoadMore: false
        }
    },
    props: ['type'],
    components: {
        cate: cate,
        goodsList: goodsList,
        crossScroll: crossScroll,
        loadMore: loadMore
    },
    mounted() {
    },
    methods: {
        initTab() {
            this.mescroll = this.$refs.homeGobal.mescroll

            if (this.flag) {
                getGlobalTab().then((rs) => {
                    this.banner = rs.banner
                    this.homeCateList = rs.list
                    this.hotList = rs.hot
                })

                getGlobalList().then((data) => {
                    this.goodsList = data
                    this.mescroll.endUpScroll(false)
                    this.showLoadMore = true
                })
                this.flag = false
            }
        }
    },
    updated() { },
    computed: {}
}

</script>
<style scoped lang="scss">

</style>
