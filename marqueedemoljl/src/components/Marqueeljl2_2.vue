<template>
  <div class="marqueeBox" ref="marqueeBox">
    <ul
      class="marqueeWrapper"
      ref="marqueeWrapper"
      :style="{ animationDuration: `${duration}s` }"
    >
      <li class="marqueeItem" v-for="item in msg" v-bind:key="item.id">
        {{ item }}
      </li>
      <li v-if="roll" :style="{ width: `${wrapperWidth}px`, color: 'red' }">
        <span class="marqueeItem" v-for="item in msg" v-bind:key="item.id">{{
          item
        }}</span>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: "Marqueeljl",
  data() {
    return {
      wrapperWidth: 0,
      itemWidth: 0,
      duration: 20,
      roll: false,
    };
  },
  props: {
    msg: {
      default: () => [],
      type: Array,
    },
    speed: {
      default: 100,
      type: Number,
    },
  },
  methods: {
    test() {
      // 测试：获取当前dom节点的样式信息window.getComputedStyle(节点元素)
      const { marqueeWrapper, marqueeBox } = this.$refs;
      console.log(window.getComputedStyle(marqueeBox).width);
      this.wrapperWidth = parseFloat(window.getComputedStyle(marqueeBox).width);
      console.log(window.getComputedStyle(marqueeWrapper).width);
      this.itemWidth = parseFloat(
        window.getComputedStyle(marqueeWrapper).width
      );
    },
    // 用于操作样式关键帧，插入规则
    insertMyRule() {
      const { marqueeWrapper, marqueeBox } = this.$refs;
      this.wrapperWidth = parseFloat(window.getComputedStyle(marqueeBox).width); // 弹幕盒子实际宽度
      this.itemWidth = parseFloat(
        window.getComputedStyle(marqueeWrapper).width
      ); // 完整所有弹幕实际宽度

      //如果 【弹幕宽度 > 展示的盒子宽度】 才开始滚动
      if (this.itemWidth > this.wrapperWidth) {
        this.roll = true;

        this.duration = this.itemWidth / this.speed;
        //console.log(document.styleSheets);
        const styleSheets = document.styleSheets[0];
        styleSheets.deleteRule(0);
        styleSheets.insertRule(
          `@keyframes moveMsg {from {transform: translateX(0);} to {transform: translateX(-${this.itemWidth}px);}}`,
          0
        );
      }
    },
  },
  mounted() {
    //this.test();
    this.insertMyRule();
  },
};
</script>

<style>
* {
  padding: 0;
  margin: 0;
}
:root {
  --line-height: 45px;
}
/* @keyframes moveMsg {
  from {
    transform: translateX(0);
  }
  to {
    transform: translateX(-2182.31px);
  }
} */
.marqueeBox {
  width: 50%;
  height: var(--line-height);
  background-color: darksalmon;
  margin: 1px auto;
  display: flex;
  border-radius: 35px;
  /* overflow: hidden; */
}
.marqueeWrapper {
  list-style-type: none;
  display: flex;
  white-space: nowrap;
  animation: moveMsg 20s infinite linear;
}
.marqueeItem {
  margin-right: 10px;
  line-height: var(--line-height);
}
</style>