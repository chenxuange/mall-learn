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
  mounted() {
    // setTimeout(this._initScroll, 20);
    this._initScroll();
  },
  methods: {
    _initScroll() {
      this.scroll = new BScroll(this.$refs.wrapper, {
        probeType: 1,
        click: true,
        pullUpLoad: true,
      });
      // console.log(this.scroll);
      // this.scroll.on("scroll", (position) => {
      //   console.log(position);
      // });
      this.scroll.on("pullingUp", () => {
        console.log("上拉加载更多");
        this.$emit("pullingUp");
      });
    },
    refresh() {
      this.scroll && this.scroll.refresh && this.scroll.refresh();
    },
  },
  watch: {
    data() {
      // this.refresh();
      console.log(this.scroll);
      // setTimeout(this.refresh, 20);
    },
  },
};
</script>

<style scoped>

</style>