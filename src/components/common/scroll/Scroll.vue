<template>
  <div class="wrapper" ref="wrapper">
    <div>
      <slot></slot>
    </div>
  </div>
</template>

<script>
// 封装BScroll
import BScroll from "better-scroll";
export default {
  name: "Scroll",
  props: {
    data: {
      type: Array,
      default() {
        return [];
      },
    },
  },
  data() {
    return {
      scroll: null,
    };
  },
  computed: {
    scrollY() {
      return this.scroll.y;
    },
  },
  mounted() {
    // 挂载时才创建对象
    setTimeout(this._initScroll, 20);
    // this._initScroll(); // error，必须先渲染组件，挂载组件时才实例化BScroll对象
  },
  methods: {
    _initScroll() {
      // 实例化scroll对象
      this.scroll = new BScroll(this.$refs.wrapper, {
        probeType: 2,
        click: true,
        pullUpLoad: true,
      });
      // this.scroll.on("scroll", (position) => {
      //   console.log(position);
      //   this.$emit("scroll", position);
      // });
      // 2.事件滚动
      this.scroll.on("scroll", (position) => {
        // console.log(position);
        this.$emit("scroll", position);
      });
      this.scroll.on("pullingUp", () => {
        // console.log("上拉加载更多");
        this.$emit("pullingUp");
      });
    },
    // 刷新框，计算框高度
    refresh() {
      this.scroll && this.scroll.refresh && this.scroll.refresh();
    },
    // 返回位置
    scrollTo(x, y, time = 300) {
      this.scroll && this.scroll.scrollTo(x, y, time);
    },
    // 保存滑动距离
    getScrollY() {
      return this.scroll.y;
    },
    // 下拉加载更多
    finishedPullUp() {
      this.scroll && this.scroll.finishedPullUp && this.scroll.finishPullUp();
    },
  },
  watch: {
    data() {
      // 监听上拉数据加载完毕后，告诉better-scroll数据已加载
      // this.scroll.refresh(); // 暂时没发现效果
      // this.scroll.finishPullUp();
      setTimeout(this.refresh, 20);
    },
  },
};
</script>

<style scoped>
</style>