<template>
    <div class="scroll-main">
        <div class="panel-item cate-list mb-10 mescroll-list" v-for="(cate,gIdx) in cateList[tabIdx].menuList" :key="gIdx">
            <div class="list-detail mb-10">
                <a v-if="cate.type =='2'" @click="$router.push($route.path +'/activity/'+ cate.rowId)" class="banner">
                    <img :src="cate.img" alt="">
                </a>
                <a v-else @click="$router.push($route.path +'/goods/list/'+ cate.id)" class="banner">
                    <img class="lazy" v-lazy="cate.img">
                </a>
                <i class="list-caret"></i>
            </div>
            <div class="list-items list-detail goods-list-detail mescroll-touch">
                <div class="list-item-md mescroll-touch-x">
                    <div class="list-primary" v-for="(goods,cIdx) in cate.goodsList" :key="cIdx" v-if="goods.goods" @click="$router.push($route.path + '/goods/detail/'+goods.goods.id)">
                        <div class="list-primary-hd"> <img class="lazy" v-lazy="goods.goods.imgCover"> </div>
                        <div class="list-primary-bd">
                            <p class="list-item-tit">{{goods.goods.title}}</p>
                            <div class="list-primary-msg">
                                <div class="list-msg-left">
                                    <p class="mcolor"> &yen; {{goods.goods.price}} </p>
                                    <p>
                                        <span class="btn-simple-min" v-show="goods.isSelfSupport == 1">自营</span>
                                        <span class="btn-simple-min coupon-tip" v-show="goods.isHasCoupon == 1">优惠券</span>
                                    </p>
                                </div>
                                <div class="list-addCart" @click.stop="showSku(goods.goods.id,goods.goods.isSku,$event)" :data-is-sku="goods.goods.isSku" :data-goods-id="goods.goods.id">
                                    <i class="iconfont icon-add"></i>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="list-primary" v-if="cate.type =='2'" @click="$router.push($route.path +'/activity/'+ cate.rowId)">
                        <div class="list-primary-hd"> <img src="http://img.sktmall.cn/uploads/home/all.jpg" alt=""> </div>
                    </div>
                    <div class="list-primary" v-else @click="$router.push($route.path +'/goods/list/'+ cate.id)">
                        <div class="list-primary-hd"> <img src="http://img.sktmall.cn/uploads/home/all.jpg" alt=""> </div>
                    </div>
                </div>
            </div>
        </div>
        <load-more :isShowLoadMore="cateList[tabIdx].showLoadMore"></load-more>
    </div>
</template>
<script>
import { common } from '@/service/common'
import { getCommonTab } from '@/api/b2cHome'
import loadMore from '@/components/common/load-more'
export default {
    props: ['tabIdx', 'cateList'],
    data() {
        return {}
    },
    components: {
        loadMore: loadMore
    },
    mounted() { },
    mixins: [common],
    methods: {
        async loadDate() {
            const idx = this.tabIdx
            const _this = this.cateList[idx].mescroll
            let data = ''
            const page = this.cateList[idx].page
            const id = this.cateList[idx].id

            data = await getCommonTab(id, page)
            this.cateList[idx].menuList = [...this.cateList[idx].menuList, ...data]
            if (data.length < 10 || data.length == 0) {
                _this.endUpScroll(false)
                _this.lockUpScroll(true)
                this.cateList[idx].showLoadMore = true
            } else {
                _this.endSuccess()
                this.cateList[idx].pages = page + 1 // 给每个竖列添加页码
            }
        },
        showSku: function (goodsId, hasSku) {
            this.goodsSku(goodsId, hasSku)
        }
    }
}

</script>
