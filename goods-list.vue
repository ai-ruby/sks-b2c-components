<template>
    <!-- 9个展示区 -->
    <div class="cate-item" :data-type="type">
        <div class="list-items mb-5" v-for="(item,cIdx) in itemList" :key="cIdx">
            <!-- <template v-if="showTitle"> -->
            <nav class="access-center title" v-if="type == 'cateGoods'">
                <div class="access-center-hd mtitle">{{item.title}}</div>
                <a @click.prevent="$router.push($route.path + '/goods/list/'+item.rowId+'/'+item.id)" class="access-center-bd stitle txtr midd">
                    <i>查看所有</i>
                    <span class="iconfont icon-caret"></span>
                </a>
            </nav>
            <nav class="bor-bottom access-center title" v-if="type == 'newGoods'">
                <div class="access-center-hd fz14 ">{{item.title}}</div>
                <a @click.prevent="$router.push($route.path + '/goods/list/'+item.id)" class="fz14 dismid access-center-bd txtr">全部</a>
            </nav>
            <!-- </template> -->
            <div class="list-detail mb-10" v-if="showBanner && type == 'global'">
                <p @click.prevent="$router.push($route.path + '/goods/list/'+item.id)" class="banner">
                    <img class="lazy" v-lazy="item.img">
                </p>
            </div>

            <div class="list-items regular-list">
                <div class="list-item-sm sublist-item">
                    <div class="list-primary" v-for="(goods,gIdx) in item.goodsList" :key="gIdx" v-if="gIdx < 9 " @click.prevent="$router.push($route.path + '/goods/detail/'+goods.id)">
                        <div class="list-primary-hd"> <img class="lazy" v-lazy="goods.imgCover"> </div>
                        <div class="list-primary-bd">
                            <p class="list-item-tit">{{goods.title}}</p>
                            <span class="mcolor">
                                    &yen; {{goods.price}}
                                </span>
                            <s class="fg12">&yen;{{goods.marketPrice}}</s>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
export default {
    data() {
        return {
            showBanner: true,
            showTitle: false
        }
    },
    props: ['itemList', 'type'],
    mounted() {
        if (this.type == 'home' && this.type == 'newGoods') {
            this.showBanner = false
        }
    },
    computed: {},
    methods: {}
}

</script>
<style scoped lang="scss">
.regular-list .list-item-sm .list-primary {
    float: left;
    margin-right: .34rem;
    &:nth-child(3n) {
        margin-right: 0;
    }
}
</style>
