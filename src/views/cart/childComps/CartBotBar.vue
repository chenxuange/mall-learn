<template>
  <div class="cart-bot-bar">
    <div class="container">
      <input
        class="check"
        type="checkbox"
        @click="checkClick"
        id="all"
        v-model="isSelectAll"
      />
      <div class="show-box"></div>
      <label for="all">全选</label>
    </div>
    <div>合计{{ totalPrice }}</div>
    <div>去结算{{ checkLength }}</div>
  </div>
</template>

<script>
import { mapGetters } from "vuex";

export default {
  name: "CartBotBar",
  components: {},
  methods: {
    checkClick() {
      //   if(this.isSelectAll) {
      //       this.cartList.forEach(ele => ele.checked = false);
      //   }else{
      //       this.cartList.forEach(ele => ele.checked = true);
      //   }
      // 思路，保存一下变量，防止同时变化影响计算属性
      const flag = !this.isSelectAll;
      this.cartList.forEach((ele) => (ele.checked = flag));
    },
  },
  computed: {
    ...mapGetters({ cartList: "cartList" }),
    // 临时写的全选按钮控制,全选反向有问题
    isSelectAll() {
      // 思路 every find filter 普通遍历
      for (let ele of this.cartList) {
        if (!ele.checked) {
          return false;
        }
      }
      return true;
    },
    totalPrice() {
      // 计算选中商品总价
      return (
        "$" +
        this.cartList
          .filter((ele) => {
            return ele.checked;
          })
          .reduce((prev, ele) => {
            return prev + ele.count * ele.price;
          }, 0)
      );
    },
    checkLength() {
      return "(" + this.cartList.filter((ele) => ele.checked).length + ")";
    },
  },
};
</script>

<style lang="less" scoped>
.cart-bot-bar {
  display: flex;
  position: fixed;
  bottom: 49px;
  left: 0;
  right: 0;
  justify-content: space-between;
  padding: 0 10px;
  height: 40px;
  font-size: 13px;
  background-color: #fff;
  border-bottom: 1px solid #ececec;
  border-top: 1px solid #ececec;
  align-items: center;

  .container {
    position: relative;
  }

  // 兼容的替代方案
  //选择紧跟input:checked的首个.show-box
  .container input:checked + .show-box {
    // background: red; // 纯底色+伪元素实现
    // 背景图片自适应大小实现，缺点是对勾大小不好控制
    background: url(~assets/img/detail/check_active.png) no-repeat center;
    background-size: cover;
    /* 这里是背景颜色，可以自定义设置 */
  }

  .container input {
    width: 20px;
    height: 20px;
    // 复选框透明，并置于上层,点击就可以触发
    opacity: 0;
    z-index: 20;
    position: relative;
  }

  .container .show-box {
    // 相同大小框置于下层，并设置好默认底色-白色
    position: absolute;
    top: 0;
    left: 0;
    width: 20px;
    height: 20px;
    border-radius: 2px;
    border: 1px solid #d8d8d8;
    /* 这里是对勾颜色，可以自定义，和勾选框背景色色差较大 */
    background: white;
  }

  // .container .show-box:before {
  //   // 通过微元素实现打狗效果
  //   content: "";
  //   position: absolute;
  //   top: 2px;
  //   left: 5px;
  //   width: 6px;
  //   height: 10px;
  //   border: solid white; // 前面始终一个白色,能盖住底色能扣出来
  //   border-width: 0 3px 3px 0;
  //   transform: rotate(45deg);
  // }

  //   .check {
  //     background: transparent;
  //     width: 18px;
  //     height: 18px;
  //     overflow: hidden;
  //     border-radius: 100%;
  //     vertical-align: bottom;
  //     border: 1px solid #ececec;
  //   }
  //   .check:checked {
  //     border: 1px solid var(----color-high-text);
  //     background: url(~assets/img/detail/check_active.png) no-repeat center;
  //     background-size: cover;
  //   }
}
/* .cart-bot-bar {
  display: flex;
  position: fixed;
  bottom: 49px;
  left: 0;
  right: 0;
  height: 40px;
  padding: 0 10px;
  font-size: 13px;
  align-items: center;
  justify-content: space-between;
  background-color: #fff;
  border-bottom: 1px solid #ececec;
  border-top: 1px solid #ececec;
  .check {
    width: 18px;
    height: 18px;
    overflow: hidden;
    border-radius: 100%;
    vertical-align: bottom;
    border: 1px solid #ececec;
  }
  .check:checked {
    border: 1px solid var(----color-high-text);
    background: url(~assets/img/detail/check_active.png) no-repeat center;
    background-size: cover;
  }
} */
</style>