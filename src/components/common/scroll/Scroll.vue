<template>
  <div class="wrapper" ref="wrapper">
    <div>
      <slot></slot>
    </div>
  </div>
</template>

<script>
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
    setTimeout(this._initScroll, 20);
    // this._initScroll(); // error，必须先渲染组件，挂载组件时才实例化BScroll对象
  },
  methods: {
    _initScroll() {
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
    refresh() {
      this.scroll && this.scroll.refresh && this.scroll.refresh();
    },
    scrollTo(x, y, time = 300) {
      this.scroll && this.scroll.scrollTo(x, y, time);
    },
    getScrollY() {
      return this.scroll.y;
    },
    finishedPullUp() {
      this.scroll && this.scroll.finishedPullUp && this.scroll.finishPullUp()
    }
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