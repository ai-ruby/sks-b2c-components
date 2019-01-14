<template>
    <!-- 首页显示 Start -->
    <mescroll ref='homeNews' class="swiper-slide stop-swiping" @upCallback="loadDate">
        <div class="list-detail mb-10">
            <div class="banner">
                <img class="lazy" v-lazy="banner" alt="">
            </div>
        </div>

        <!-- 9个商品 -->
        <goods-list :itemList="goodsList" :type="'newGoods'"></goods-list>

        <load-more :isShowLoadMore="showLoadMore"></load-more>
    </mescroll>
    <!-- 首页显示 End -->
</template>
<script>
import loadMore from '@/components/common/load-more'
import { getNewTab, getNewList } from '@/api/b2cHome'
import cate from '@/components/b2c/cate'
import crossScroll from '@/components/b2c/cross-scroll'
import goodsList from '@/components/b2c/goods-list'

export default {
    data() {
        return {
            flag: true,
            banner: '',
            goodsList: '',
            mescroll: '',
            page: 1,
            len: 7,
            showLoadMore: false
        }
    },
    components: {
        cate: cate,
        goodsList: goodsList,
        crossScroll: crossScroll,
        loadMore: loadMore
    },
    mounted() { },
    methods: {
        initTab() {
            this.mescroll = this.$refs.homeNews.mescroll

            if (this.flag) {
                getNewTab().then((rs) => {
                    this.banner = rs.banner
                })

                this.flag = false
                this.loadDate()
            }
        },
        loadDate() {
            if (!this.flag) {
                const page = this.page
                getNewList(page).then((data) => {
                    if (data.length < this.len || data.length == 0) {
                        this.mescroll.endUpScroll(false)
                        this.mescroll.lockUpScroll(true)
                        this.showLoadMore = true
                    }
                    this.mescroll.endSuccess()
                    this.goodsList = [...this.goodsList, ...data]
                    this.page = page + 1
                })
            }
        }
    }
}

</script>
<style scoped lang="scss">

</style>
