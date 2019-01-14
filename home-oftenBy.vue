<template>
    <mescroll ref="oftenBy" class="swiper-slide stop-swiping" @upCallback="loadDate">
        <!-- 我的常购清单 -->
        <div class="list-detail mb-10" v-if="oftenBuyList != ''">
            <div class="list-items">
                <div class="panel-title midd">
                    <i class="iconfont icon-h-log"></i>
                    <span>我的常购清单</span>
                </div>
                <div class="sublist-item list-item-lg">
                    <div class="list-primary" v-for="(goods,index) in oftenBuyList" :data-index="index" :key="index" @click="$router.push($route.path + '/goods/detail/'+goods.id)" v-if="index > 3">
                        <div class="list-primary-hd"> <img class="lazy" v-lazy="goods.imgCover"> </div>
                        <div class="list-primary-bd">
                            <p class="list-item-tit">{{goods.title}}</p>
                            <div class="list-primary-msg">
                                <div class="list-msg-left">
                                    <p class="mcolor"> &yen; {{goods.price}} </p>
                                    <p>
                                        <span class="btn-simple-min" v-show="goods.isSelfSupport == 1">自营</span>
                                        <span class="btn-simple-min coupon-tip" v-show="goods.isHasCoupon == 1">优惠券</span>
                                    </p>
                                </div>
                                <div class="list-addCart" @click.stop="showSku(goods.id,goods.isSku, $event)" :data-is-sku="goods.isSku" :data-goods-id="goods.id">
                                    <i class="iconfont icon-add"></i>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- 热销商品 -->
        <div class="list-detail mb-10" v-if="topSaleList != ''">
            <div class="list-items">
                <div class="panel-title midd">
                    <i class="iconfont icon-h-hot"></i>
                    <span>热销商品</span>
                </div>
                <div class="sublist-item list-item-lg">
                    <div class="list-primary" v-for="(goods,index) in topSaleList" :data-index="index" :key="index" @click="$router.push($route.path + '/goods/detail/'+goods.id)" v-if="index > 3">
                        <div class="list-primary-hd"> <img class="lazy" v-lazy="goods.imgCover"> </div>
                        <div class="list-primary-bd">
                            <p class="list-item-tit">{{goods.title}}</p>
                            <div class="list-primary-msg">
                                <div class="list-msg-left">
                                    <p class="mcolor"> &yen; {{goods.price}} </p>
                                    <p>
                                        <span class="btn-simple-min" v-show="goods.isSelfSupport == 1">自营</span>
                                        <span class="btn-simple-min coupon-tip" v-show="goods.isHasCoupon == 1">优惠券</span>
                                    </p>
                                </div>
                                <div class="list-addCart" @click.stop="showSku(goods.id,goods.isSku, $event)" :data-is-sku="goods.isSku" :data-goods-id="goods.id">
                                    <i class="iconfont icon-add"></i>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- 猜你喜欢 -->
        <div class="list-detail" v-if="likeList != ''">
            <div class="list-items">
                <div class="panel-title midd">
                    <i class="iconfont icon-h-like"></i>
                    <span>猜你喜欢</span>
                </div>
                <div class="sublist-item list-item-lg">
                    <div class="list-primary" v-for="(goods,index) in likeList" :data-index="index" :key="index" @click="$router.push($route.path + '/goods/detail/'+goods.id)" v-if="index > 3">
                        <div class="list-primary-hd"> <img class="lazy" v-lazy="goods.imgCover"> </div>
                        <div class="list-primary-bd">
                            <p class="list-item-tit">{{goods.title}}</p>
                            <div class="list-primary-msg">
                                <div class="list-msg-left">
                                    <p class="mcolor"> &yen; {{goods.price}} </p>
                                    <p>
                                        <span class="btn-simple-min" v-show="goods.isSelfSupport == 1">自营</span>
                                        <span class="btn-simple-min coupon-tip" v-show="goods.isHasCoupon == 1">优惠券</span>
                                    </p>
                                </div>
                                <div class="list-addCart" @click.stop="showSku(goods.id,goods.isSku, $event)" :data-is-sku="goods.isSku" :data-goods-id="goods.id">
                                    <i class="iconfont icon-add"></i>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <load-more :isShowLoadMore="showLoadMore"></load-more>
    </mescroll>
    <!-- 首页显示 End -->
</template>
<script>
import loadMore from '@/components/common/load-more'
import { getLoadOftenBuy, getTopSale, getSameCate } from '@/api/b2cHome'
import { common } from '@/service/common'
import { hideLoading } from '@/service/model'
export default {
    data() {
        return {
            oftenBuyList: [],
            topSaleList: [],
            likeList: [],
            flag: true,
            cateIds: '',
            goodIds: '',
            showLoadMore: false
        }
    },
    mixins: [common],
    components: {
        loadMore: loadMore
    },
    methods: {
        async initTab() {
            this.mescroll = this.$refs.oftenBy.mescroll

            if (this.flag) {
                // 常购清单加载
                this.oftenBuyList = await getLoadOftenBuy()

                // 热销商品加载
                this.topSaleList = await getTopSale()
                hideLoading()

                const arr = []
                // 查找是否有常购清单
                if (this.oftenBuyList.length == 0) {
                    // 没有的则在热销获取cateId
                    this.topSaleList.filter((item) => {
                        arr.push(item.cateId)
                    })
                } else {
                    // 有买过的商品的
                    this.oftenBuyList.filter((item) => {
                        arr.push(item.cateId)
                    })
                }
                arr.filter((item, index, self) => {
                    if (self.indexOf(item) == index ) {
                        this.cateIds += item + ','
                    }
                })

                this.cateIds = this.cateIds.substring(0, this.cateIds.length - 1)

                this.flag = false
                this.loadDate()
            }
        },
        showSku: function (goodsId, isSku) {
            this.goodsSku(goodsId, isSku)
        },
        loadDate () {
            if (!this.flag) {
                const params = {
                    cateIds: this.cateIds,
                    goodIds: this.goodIds
                }
                getSameCate(params).then((data) => {
                    if (data.length < 10 || data.length == 0) {
                        this.mescroll.endUpScroll(false)
                        this.mescroll.lockUpScroll(true)
                        this.showLoadMore = true
                        this.mescroll.upwarp.style.display = 'none'
                    }
                    this.mescroll.endSuccess(data.length)
                    this.likeList = [...this.likeList, ...data]
                    this.likeList.filter((item) => {
                        this.goodIds += item.id + ','
                    })
                    this.goodIds = this.goodIds.substring(0, this.goodIds.length - 1)
                    this.mescroll.upwarp.style.display = 'block'
                })
            }
        }
    }
}
</script>
<style scoped lang="scss">
.panel-title {
    padding-top: 0.3rem;
    padding-left: 0.38rem;
    padding-bottom: 0.2rem;
    color: #ff5252;
    font-size: 0.3rem;
    .iconfont {
        font-size: 0.4rem;
    }
}
.list-items {
    .panel-title + .list-item-lg {
        padding: 0.44rem;
        padding-top: 0;
        padding-bottom: 0;
    }
}
</style>
