<template>
  <div class="detail">
    <detail-nav-bar class="detail-nav" />
    <scroll class="content" ref="scroll" @scroll="contentScroll">
      <!-- 轮播图 -->
      <detail-swiper :images="topImages"></detail-swiper>
      <!-- 基础 -->
      <detail-base-info :goods="goods"></detail-base-info>
      <!-- 店铺 -->
      <detail-shop-info :shop="shop"></detail-shop-info>
      <!-- 详情 -->
      <detail-goods-info
        :detailInfo="detailInfo"
        @imgLoaded="imgLoaded"
      ></detail-goods-info>
      <!-- 参数 -->
      <detail-param-info :paramInfo="paramInfo" ref="param" />
      <!-- 评论 -->
      <detail-comment-info :commentInfo="commentInfo" ref="comment" />
      <!-- 推荐 -->
      <goods-list  :goods="goodsList" ref="recommend" />
    </scroll>
    <back-top v-show="this.showBackTop" />
  </div>
</template>

<script>
// 导入组件
import DetailNavBar from "./childComps/DetailNavBar.vue";
import Scroll from "components/common/scroll/Scroll";
import DetailSwiper from "./childComps/DetailSwiper.vue";
import DetailBaseInfo from "./childComps/DetailBaseInfo.vue";
import DetailShopInfo from "./childComps/DetailShopInfo";
import DetailGoodsInfo from "./childComps/DetailGoodsInfo";
import DetailParamInfo from "./childComps/DetailParamInfo";
import DetailCommentInfo from "./childComps/DetailCommentInfo";
import GoodsList from "components/content/goods/GoodsList";

import BackTop from 'components/content/backTop/BackTop'
// 导入请求
import {
  getDetail,
  Goods,
  Shop,
  GoodsParam,
  getRecommend,
} from "network/detail";
export default {
  name: "Detail",
  data() {
    return {
      iid: "",
      topImages: [],
      goods: {},
      shop: {},
      detailInfo: {},
      paramInfo: {},
      commentInfo: {},
      goodsList: [],
      themeTops: [], // 各区域位置

      showBackTop: false

    };
  },
  components: {
    DetailNavBar,
    Scroll,
    DetailSwiper,
    DetailBaseInfo,
    DetailShopInfo,
    DetailGoodsInfo,
    DetailParamInfo,
    DetailCommentInfo,
    GoodsList,
    BackTop,
  },
  created() {
    //1.取出iid
    this.iid = this.$route.query.iid;
    //2.发送商品请求
    this.__getDetail(this.iid);
    //3.发送推荐请求
    this.__getRecommend();
  },
  mounted() {
    console.log(this.$refs.scroll);
  },
  methods: {
    __getDetail(iid) {
      //向后台请求
      getDetail(iid).then((res) => {
        //1.获取数据
        const data = res.result;
        console.log(res);
        // 2.获取顶部的图片数据
        this.topImages = data.itemInfo.topImages;
        // 3.获取商品信息
        this.goods = new Goods(
          data.itemInfo,
          data.columns,
          data.shopInfo.services
        );
        // 4.获取店铺信息
        this.shop = new Shop(data.shopInfo);
        // 5.获取商品详情信息
        this.detailInfo = data.detailInfo;

        // 6.保存参数信息
        this.paramInfo = new GoodsParam(
          data.itemParams.info,
          data.itemParams.rule
        );
        // 7.保存评论数据
        if (data.rate.list) {
          this.commentInfo = data.rate.list[0];
        }
      });
    },
    __getRecommend() {
      getRecommend().then((res) => {
        console.log(res);
        this.goodsList = res.data.list;
      });
    },
    // 滚动事件
    contentScroll(position) {
        console.log(position);
        this.showBackTop = position.y <= -1000;

        // todo:

    },
    imgLoaded() {
      this.$refs.scroll.refresh();
      console.log("refresh");
      this.themeTops = []
      this.themeTops.push(0);
      this.themeTops.push(this.$refs.param.$el.offsetTop);
      this.themeTops.push(this.$refs.comment.$el.offsetTop);
      this.themeTops.push(this.$refs.recommend.$el.offsetTop);
      console.log(this.themeTops);

    },
  },
};
</script>

<style scoped>
#detail {
  position: relative;
  z-index: 9;
  background-color: #fff;
  height: 100vh;
}

.detail-nav {
  position: relative;
  z-index: 9;
  background-color: #fff;
}

.content {
  /* 暂时绝对定位脱标控制wrapper高度 */
  position: absolute;
  height: calc(100% - 44px);
  /* height: 600px; */
}
</style>