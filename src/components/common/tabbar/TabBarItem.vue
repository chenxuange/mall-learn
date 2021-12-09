<template>
  <div class="tab-bar-item" @click="itemClick">
      <div v-if="!isAcitve"><slot name="item-icon"></slot></div>
      <div v-else><slot name="item-icon-active"></slot></div>
      <!-- 插槽不建议直接设置样式 -->
      <!-- <div :class="{'active': isAcitve}"><slot name="item-text"></slot></div> -->
      <div :style="activeStyle"><slot name="item-text"></slot></div>

  </div>
</template>

<script>
export default {
  name: 'TabBarItem',
  props: {
    link: String, // 传入的路径
    activeColor: {
      type: String,
      default: 'red'
    }
  },
  data() {
      return {
          // isAcitve: true,
          path: this.link  // 路径存储
      }
  },
  computed: {
    isAcitve() {
      // console.log("---")
      // console.log(this.path);  // 路由路径
      // console.log(this.$route.path);  // 实际路径
      return this.$route.path.indexOf(this.path) !== -1;
    },
    activeStyle() {
      return this.isAcitve ? {'color': this.activeColor} : {}
    }
  },
  methods: {
    itemClick() {
      this.$router.replace(this.path);  // 这种模式无法退回
    }
  }

}

</script>

<style scoped>
    .tab-bar-item {
    flex: 1;
    text-align: center;
    height: 49px;
    font-size: 12px;
  }
  .tab-bar-item img {
      width: 29px;
      height: 29px;
  }

  .active {
      color: red;
      /* color: @activeColor;  暂时没弄懂  */
  }
</style>