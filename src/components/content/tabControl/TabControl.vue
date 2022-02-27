<template>
  <div class="tab-control">
    <div
      class="tab-control-item"
      v-for="(item, index) in titles"
      :key="index"
      :class="{ active: currentIndex == index }"
      @click="itemClick(index)"
    >
      <span>{{ item }}</span>
    </div>
  </div>
</template>

<script>
export default {
  name: "TabControl",
  props: {
    titles: {
      type: Array,
      default() {
        return [];
      },
    },
  },
  data() {
    return {
      currentIndex: 0 // 默认tab索引
    };
  },
  methods: {
    // 点击切换tab，同时给父组件传递索引值
    itemClick(index) {
      this.currentIndex = index;
      this.$emit('tabClick', index);
    },
  },
};
</script>

<style scoped>
.tab-control {
  display: flex;
  height: 40px;
  line-height: 40px;
  text-align: center;
}
.tab-control-item {
  flex: auto;
}
.tab-control-item span {
  padding: 5px;
}
.active span {
  color: var(--color-high-text);
  border-bottom: 2px solid var(--color-high-text);
}
</style>