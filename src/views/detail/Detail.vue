<template>
  <div class="detail">
      <detail-nav-bar></detail-nav-bar>
      <detail-swiper :images='topImages'></detail-swiper>
      <!-- 商品 -->
      <detail-base-info :goods="goods"></detail-base-info>
      <detail-shop-info :shop="shop"></detail-shop-info>
      <detail-goods-info :detailInfo="detailInfo"></detail-goods-info>
      <!-- 参数 -->
      <detail-param-info :paramInfo="paramInfo"></detail-param-info>
      <!-- 评论 -->
      <detail-comment-info :commentInfo="commentInfo"></detail-comment-info>
      <!-- 推荐 -->
      <goods-list :goods="goodsList"></goods-list>
  </div>

</template>

<script>
// 导入组件
import DetailNavBar from './childComps/DetailNavBar.vue'
import DetailSwiper from './childComps/DetailSwiper.vue'
import DetailBaseInfo from './childComps/DetailBaseInfo.vue'
import DetailShopInfo from './childComps/DetailShopInfo'
import DetailGoodsInfo from './childComps/DetailGoodsInfo'
import DetailParamInfo from './childComps/DetailParamInfo';
import DetailCommentInfo from './childComps/DetailCommentInfo'

import GoodsList from 'components/content/goods/GoodsList'
// 导入请求
import {getDetail, Goods, Shop, GoodsParam, getRecommend} from 'network/detail';
export default {
  name: 'Detail',
  data () {
      return {
          iid: '',
          topImages: [],
          goods: {},
          shop: {},
          detailInfo: {},
          paramInfo: {},
          commentInfo: {},

          goodsList: [],
      }
  },
  components: {
      DetailNavBar,
      DetailSwiper,
      DetailBaseInfo,
      DetailShopInfo,
      DetailGoodsInfo,
      DetailParamInfo,
      DetailCommentInfo,

      GoodsList
    },
  created () {
      //1.取出iid
      this.iid = this.$route.query.iid;
      //2.发送商品请求
      this.__getDetail(this.iid);
      //3.发送推荐请求
      this.__getRecommend();
  },
  methods: {
    __getDetail(iid) {
        //向后台请求
        getDetail(iid).then(res => {
            //1.获取数据
            const data = res.result;
            console.log(res);
            // 2.获取顶部的图片数据
            this.topImages = data.itemInfo.topImages;
            // 3.获取商品信息
            this.goods = new Goods(data.itemInfo, data.columns, data.shopInfo.services);
            // 4.获取店铺信息
            this.shop = new Shop(data.shopInfo);
            // 5.获取商品详情信息
            this.detailInfo = data.detailInfo;

            // 6.保存参数信息
            this.paramInfo = new GoodsParam(data.itemParams.info, data.itemParams.rule);
            // 7.保存评论数据
            if(data.rate.list) {
                this.commentInfo = data.rate.list[0];
            }
        })
    },
    __getRecommend() {
        getRecommend().then(res => {
            console.log(res);
            this.goodsList = res.data.list;
        })
    }
    }
}

</script>

<style scoped>
  
</style>