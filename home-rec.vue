<template>
    <mescroll ref="homeRec" class="swiper-slide stop-swiping" @upCallback="loadDate">
        <skeleton-screen v-show="showSkeleton"></skeleton-screen>
        <!-- 首页显示 Start -->
        <div class="cate-one" v-show="!showSkeleton">
            <!-- banner + 分类栏 -->
            <cate v-if="banner" :cateList="homeCateList" :isCheckSubscribe="isCheckSubscribe" :type="'home'">
                <div slot="swiperBanner">
                    <swiper :options="swiperOption" ref="mySwiper" v-if="isSwiper">
                        <swiper-slide v-for="(item, idx) in banner" :key="idx">
                            <a @click="bannerJump(item.link)" v-if="item.link != ''"><img :src="item.img" alt=""></a>
                            <a v-else><img :src="item.img" alt=""></a>
                        </swiper-slide>
                        <div class="swiper-pagination" slot="pagination"></div>
                    </swiper>
                    <img class="lazy" :src="banner" alt="" v-else>
                </div>
            </cate>

            <div class="list-detail">
                <!-- 推荐下的限时购和优惠券入口 -->
                <div class="discount-list" v-if="flashList != null ">
                    <div class="panel-media-meta">
                        <div class="btn-simple-min">限时购</div>
                        <div class="panel-media meta-one" @click.prevent="showFlashSale()">
                            <div class="panel-media-hd">
                                <img v-lazy="flashList.img" alt="">
                            </div>
                            <div class="panel-media-bd">
                                <div class="panel-media-meta">
                                    <div class="panel-media-tit meta-one text-hide2 fz12">
                                        {{flashList.title}}
                                    </div>
                                </div>
                                <div class="panel-media-meta">
                                    <div class="meta-one fg12">
                                        <span class="fz14 f90 mr-5">&yen;{{flashList.price}}
                                        </span> 原价:
                                        <s> &yen; {{flashList.marketPrice}}</s>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <img class="coupons-img mr-20 lazy" v-lazy="couponsImg" alt="" @click.prevent="$router.push('/coupons/index')" />
                    </div>
                </div>
                <!-- 邀请有礼入口 -->
                <!-- <div class="panel-medias pb-10">
                    <div class="banner" @click.prevent="$router.push('/gift/newsUserIndex')">
                        <img class="lazy" v-lazy="giftImg" alt="">
                    </div>
                </div> -->
            </div>

        </div>

        <!-- 通用tab -->
        <div class="panel-item cate-list mb-10 mescroll-list" v-for="(cate,gIdx) in cateList" :key="gIdx">
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
                                    <p class="mcolor">
                                        &yen; {{goods.goods.price}}
                                    </p>
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
                        <div class="list-primary-hd">
                            <img src="http://img.sktmall.cn/uploads/home/all.jpg" alt="">
                        </div>
                    </div>
                    <div class="list-primary" v-else @click="$router.push($route.path +'/goods/list/'+ cate.id)">
                        <div class="list-primary-hd">
                            <img src="http://img.sktmall.cn/uploads/home/all.jpg" alt="">
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- 猜你喜欢 -->
        <div class="list-detail" >
            <div class="list-items">
                <div class="panel-like midd">
                    <i class="iconfont icon-favhl"></i>
                    <span>猜你喜欢</span>
                </div>
                <div class="sublist-item list-item-lg">
                    <div class="list-primary"  v-for="(goods,index) in likeList" :data-index="index" :key="index" @click="$router.push($route.path + '/goods/detail/'+goods.id)">
                        <div class="list-primary-hd"> <img class="lazy" v-lazy="goods.imgCover"> </div>
                        <div class="list-primary-bd">
                            <p class="list-item-tit">{{goods.title}}</p>
                            <div class="list-primary-msg">
                                <div class="list-msg-left">
                                    <p class="mcolor"> &yen; {{goods.price}} </p>
                                    <p> <span class="btn-simple-min" v-show="goods.isSelfSupport == 1">自营</span> <span class="btn-simple-min coupon-tip" v-show="goods.isHasCoupon == 1">优惠券</span> </p>
                                </div>
                                <div class="list-addCart" @click.stop="showSku(goods.id,goods.isSku, $event)" :data-is-sku="goods.isSku" :data-goods-id="goods.id"> <i class="iconfont icon-add"></i> </div>
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
import { checkSubscribe } from '@/api/user'
import { getRecTab, getRecTabList, getRecLikeList } from '@/api/b2cHome'
import cate from '@/components/b2c/cate'
import { common } from '@/service/common'
import skeletonScreen from '@/components/common/SkeletonScreen/SkeletonScreenTemplate/homeIndex'
import { showLoading, hideLoading } from '@/service/model'
export default {
    data() {
        return {
            banner: '',
            homeCateList: '',
            isCheckSubscribe: '',
            giftImg: '',
            couponsImg: '',
            flashList: '',
            cateList: [],
            likeList: [],
            page: true,
            len: '',
            showLoadMore: false,
            flag: true,
            homeRecTab: '',
            showLike: false,
            showSkeleton: true,
            swiperOption: {
                notNextTick: true,
                autoplay: 3000,
                loop: true,
                pagination: '.swiper-pagination'
            },
            subscribeImg: {
                img: ''
            },
            // 是否显示轮播图
            isSwiper: false
        }
    },
    mixins: [common],
    components: {
        cate: cate,
        loadMore: loadMore,
        skeletonScreen: skeletonScreen
    },
    created () {
        showLoading()
    },
    methods: {
        showSku: function (goodsId, isSku) {
            this.goodsSku(goodsId, isSku)
        },
        showFlashSale: function () {
            this.$parent.$parent.menuTab(2)
        },
        initTab() {
            this.mescroll = this.$refs.homeRec.mescroll
            if (this.flag) {
                this.loadTab()
            }
        },
        // 是否有关注公众号
        async loadTab() {
            const check = await checkSubscribe()
            this.isCheckSubscribe = check.isSubscribe != '1'
            this.subscribeImg.img = check.subscribeImg
            // 头部
            const rs = await getRecTab()
            this.banner = rs.banner
            this.homeCateList = rs.iconList
            this.flashList = rs.flashSaleGoods
            this.couponsImg = rs.newUser
            this.giftImg = rs.coupon

            // 头部banner处理
            const isArr = this.banner instanceof Array && this.banner.length > 1
            // 首页
            if (isArr && this.isCheckSubscribe) {
                // 数组and未关注
                this.isSwiper = true
                this.banner = [this.subscribeImg, ...this.banner]
            } else if (isArr && !this.isCheckSubscribe) {
                // 数组已经关注
                this.isSwiper = true
                this.banner = this.banner
            } else if (!isArr && this.isCheckSubscribe) {
                // 非数组未关注
                this.isSwiper = true
                this.banner = [this.subscribeImg, ...this.banner]
            } else {
                this.banner = this.banner[0].img
                this.isSwiper = false
            }

            // 先加载以上的后加载通用滑动模块
            this.flag = false
            this.loadDate()
        },
        loadDate() {
            if (!this.flag) {
                if (this.page) {
                    getRecTabList().then((data) => {
                        // if (data.list.length < len || data.list.length == 0) {
                        // this.mescroll.endUpScroll(false)
                        // this.mescroll.lockUpScroll(true)
                        // this.showLoadMore = true
                        // }
                        this.cateList = [...this.cateList, ...data.list]
                        hideLoading()
                        this.flag = false
                        this.page = false
                        this.showSkeleton = false
                    })
                }
                this.mescroll.upwarp.style.display = 'block'
                // 加载猜你喜欢
                this.loadLikeDate()
            }
        },
        loadLikeDate() {
            getRecLikeList().then((data) => {
                this.likeList = [...this.likeList, ...data]
                // console.log(data,this.likeList);
                this.mescroll.endSuccess(data.length)
                hideLoading()
            })
        },
        bannerJump(link) {
            if (link.indexOf('b2c/home/index?tab=') > 0) {
                // 跳转tab
                const arr = link.split('tab=')
                const tab = arr.pop()
                this.$parent.$parent.menuTab(tab)
            } else {
                this.$router.push(link)
            }
        }
    }
}
</script>
<style scoped lang="scss">
.panel-like  {
    text-align: center;
    padding-top: .3rem;
    padding-bottom: .2rem;
    color: #FF5252;
    .iconfont {
        font-size: .22rem;
        color: #fff;
        border-radius: 50%;
        width: .4rem;
        height: .4rem;
        line-height: .45rem;
        background-color: #FF5252;
    }
}
.list-items {
    .panel-like + .list-item-lg {
        padding-top: 0;
    }
}
</style>
