<template>
  <div id="home">
    <nav-bar class="home-nav">
      <div slot="center">购物街</div>
    </nav-bar>
    <!-- tab切换栏的吸顶效果是通过条件显隐展示的 -->
    <tab-control
      ref="topTab"
      v-show="showTabControl"
      class="tab-control"
      :titles="['流行', '新款', '精选']"
      @tabClick="tabClick"
    />
    <scroll
      class="content"
      ref="scroll"
      @pullingUp="loadMore"
      @scroll="contentScroll"
      :data="showGoodsList"
    >
      <home-swiper :banners="banners" @imgLoaded="swiperLoaded" />
      <home-recomend :recommends="recommends" />
      <feature-view />
      <tab-control
        ref="contentTab"
        class="tab-control"
        :titles="['流行', '新款', '精选']"
        @tabClick="tabClick"
      />
      <goods-list :goods="showGoodsList" />
    </scroll>
    <back-top v-show="showBackTop" @click.native="backTop" />
  </div>
</template>

<script>
// 导入组件
import NavBar from "components/common/navbar/NavBar";
import Scroll from "components/common/scroll/Scroll";
import HomeSwiper from "./childComps/HomeSwiper";
import HomeRecomend from "./childComps/HomeRecomend";
import FeatureView from "./childComps/FeatureView";
import TabControl from "components/content/tabControl/TabControl";
import GoodsList from "components/content/goods/GoodsList";
import BackTop from "components/content/backTop/BackTop";

// 导入路由
import { getHomeMultidata, getHomeGoods } from "network/home";
import { TOP_DISTANCE } from "common/const";
export default {
  name: "Home",
  components: {
    NavBar,
    HomeSwiper,
    HomeRecomend,
    FeatureView,
    TabControl,
    GoodsList,
    BackTop,
    Scroll,
  },
  data() {
    return {
      banners: [],
      recommends: [],
      goods: {
        pop: { page: 0, list: [] },
        new: { page: 0, list: [] },
        sell: { page: 0, list: [] },
      },
      currentType: "pop",
      showBackTop: true,
      showTabControl: false,
      offsetTop: 0,
      saveY: 0,
    };
  },
  computed: {
    showGoodsList() {
      return this.goods[this.currentType].list;
    },
  },
  created() {
    // 请求轮播数据
    this._getHomeMultidata();
    // 请求商品数据
    this._getHomeGoods("pop");
    this._getHomeGoods("new");
    this._getHomeGoods("sell");
  },
  mounted() {
    const refresh = this.debounce(this.$refs.scroll.refresh, 500);
    // 监听一些事件
    this.$bus.$on("imgLoad", () => {
      // console.log("refresh");
      // this.$refs.scroll.refresh();
      refresh();
    });
  },
  destroyed() {
    console.log("home destroyed");
  },
  activated() {
    // 和生命周期有关
    // console.log("activated");
    // 初始没有bscroll对象
    // console.log(this.$refs.scroll.scroll); // null
    // console.log(this.$refs.scroll);
    // console.log("滑动到上次退出位置")
    this.$refs.scroll.scrollTo(0, this.saveY, 0);
  },
  deactivated() {
    console.log("deactivated");
    this.saveY = this.$refs.scroll.getScrollY();
  },
  methods: {
    /**
     * 防抖动函数
     */
    debounce(func, delay) {
      let timer = null;
      return function (...args) {
        if (timer) clearTimeout(timer);
        timer = setTimeout(() => {
          func.apply(this, args);
          // console.log("refresh");
        }, delay);
      };
    },
    /**
     * 网络请求的相关方法
     */
    _getHomeMultidata() {
      getHomeMultidata().then((res) => {
        this.banners = res.data.banner.list;
        this.recommends = res.data.recommend.list;
      });
    },
    _getHomeGoods(type) {
      const page = this.goods[type].page + 1;
      getHomeGoods(type, page).then((res) => {
        const newList = res.data.list;
        this.goods[type].list.push(...newList);
        this.goods[type].page += 1;

        // 完成上拉加载更多
        this.$refs.scroll.finishedPullUp();
      });
    },

    /**
    事件监听的相关方法
     */
    tabClick(index) {
      switch (index) {
        case 0:
          this.currentType = "pop";
          break;
        case 1:
          this.currentType = "new";
          break;
        case 2:
          this.currentType = "sell";
          break;
        default:
          this.currentType = "pop";
      }
      // 将两个tabControl同步
      this.$refs.topTab.currentIndex = index;
      this.$refs.contentTab.currentIndex = index;
    },
    contentScroll(position) {
      // console.log(position)
      // 判断展示backTop
      this.showBackTop = position.y <= -TOP_DISTANCE;
      // 判断显示吸顶效果
      this.showTabControl = position.y <= -this.offsetTop;
    },
    swiperLoaded() {
      this.offsetTop = this.$refs.contentTab.$el.offsetTop;
      // console.log(this.offsetTop);
    },

    backTop() {
      console.log("backTop");
      // 不要直接调子组件的原生方法，简单包装一下
      // this.$refs.scroll.scroll.scrollTo(0, 0);
      // console.log(this.$refs.scroll.scroll.scrollTo);
      this.$refs.scroll.scrollTo(0, 0);
    },
    loadMore() {
      console.log("home load more");
      this._getHomeGoods(this.currentType);
    },
  },
};
</script>

<style scoped>
#home {
  height: 100vh;
  position: relative;
}

.home-nav {
  background-color: var(--color-tint);
  color: #fff;
  position: relative;
  z-index: 9;
}

.tab-control {
  /* position: relative; */
  z-index: 9;
}

/* 这个很关键，top和bottom决定了wrapper高度小于滑动区域scrollerHeight */
.content {
  position: absolute;
  top: 44px;
  bottom: 49px;
  left: 0;
  right: 0;
}
</style>