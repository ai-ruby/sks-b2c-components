<template>
    <mescroll ref='mchTab' @upCallback="showTabList" class="swiper-slide stop-swiping">
        <div class="mch-panel menu-list mescroll-touch-x">
            <span :class="[curTabIdx == index ? 'on': '','btn-lightgray-min']" v-for="(tab, index) in mchTabList" :key="index" @click="menuTab(tab.id,index)">{{tab.title}}</span>
        </div>
        <div v-for="(tab, index) in mchTabList" :key="index" v-show="curTabIdx == index">
            <div class="mb-5 panel-medias" v-for="(mch, mchIdx) in tab.mchList" :key="mchIdx" @click="$router.push($route.path + '/merchant/detail/'+ mch.rowId)" v-if="tab.mchList">
                <div class="list-detail goods-list">
                    <div class="banner">
                        <img class="lazy" v-lazy="mch.img">
                    </div>
                    <div class="fz14 txtc threeColor mb-5 mt-10">
                        <span>{{mch.title}}</span>
                    </div>
                    <div class="f126 text-hide txtc mb-10">{{mch.subtitle}}</div>
                </div>
                <div class="regular-list mch-list">
                    <div class="list-item-sm">
                        <div class="list-primary flex-one" v-for="(mchGoods, mchGoodsIdx) in mch.goodsList" :key="mchGoodsIdx">
                            <div class="list-primary-hd">
                                <div class="btn-base on hot-tip" v-if="mchGoods.isHot=='1'">当日热销</div>
                                <img class="lazy" v-lazy="mchGoods.imgCover">
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <loading v-if="tab.loading"></loading>
            <load-more :isShowLoadMore="tab.showLoadMore"></load-more>
        </div>
    </mescroll>
</template>
<script>
import { getMchTab, getHomeMchList } from '@/api/b2cHome'
import loadMore from '@/components/common/load-more'
import loading from '@/components/common/loading'
export default {
    data() {
        return {
            flag: true,
            mchList: '',
            banner: '',
            mchTabList: [],
            curTabIdx: 0,
            curTabId: '',
            isLoaded: false
        }
    },
    props: ['isLoad'],
    components: {
        loading: loading,
        loadMore: loadMore
    },
    mounted() {},
    methods: {
        initTab() {
            this.mescroll = this.$refs.mchTab.mescroll
            this.isLoaded = true
            this.showTabList()
        },
        menuTab: function(id, idx) {
            if (this.curTabIdx === idx) {
                return
            }
            this.curTabIdx = idx
            this.curTabId = id

            this.showTabList()
        },
        async showTabList() {
            // 限制第一次进来首页加载此组件
            if (!this.isLoaded) return
            // 第一次进来加载
            if (this.flag) {
                const rs = await getMchTab()
                this.mchTabList = rs
                for (const idx in this.mchTabList) {
                    this.$set(this.mchTabList[idx], 'mchList', [])
                    this.$set(this.mchTabList[idx], 'page', 1)
                    this.$set(this.mchTabList[idx], 'showLoadMore', false)
                    this.$set(this.mchTabList[idx], 'loading', true) // 是否现在加载loading
                    this.$set(this.mchTabList[idx], 'isReturn', false)
                }
                this.curTabId = rs[this.curTabIdx].id
                this.flag = false
            }
            const _this = this.mescroll
            const id = this.curTabId
            const idx = this.curTabIdx
            const page = this.mchTabList[idx].page
            if (this.mchTabList[idx].isReturn) {
                return
            }

            getHomeMchList(id, page).then((rs) => {
                this.mchTabList[idx].mchList = [...this.mchTabList[idx].mchList, ...rs]
                this.mchTabList[idx].loading = false
                if (rs.length < 4 || rs.length == 0) {
                    this.mchTabList[idx].showLoadMore = true
                    this.mchTabList[idx].isReturn = true
                    _this.endUpScroll(false)
                }

                _this.endSuccess()
                this.mchTabList[idx].page = page + 1 // 给每个竖列添加页码
            })
        }
    }
}

</script>
<style scoped lang="scss">
.mch-panel {
    height: 0.9rem;
    padding-left: 0.3rem;
    padding-right: 0.3rem;
}

.btn-lightgray-min {
    margin-right: 0.2rem;
    margin-top: 0.2rem;
    margin-bottom: 0.2rem;
}
.loading-logo {
    width: 3rem;
    left: 50%;
    margin-left: -1.5rem;
    top: 50%;
    margin-top: -2rem;
}
</style>
