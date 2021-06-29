<template>
  <div class="marqueeBox">
    <ul class="marqueeWrapper">
      <li class="marqueeItem" v-for="item in msg" v-bind:key="item.id">
        {{ item }}
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: "Marqueeljl",
  data() {
    return;
  },
  props: {
    msg: {
      default: () => [],
      type: Array,
    },
  },
};
</script>

<style>
* { padding: 0; margin: 0; }
:root{
    --line-height:45px;
}
@keyframes moveMsg {
  from {
    transform: translateX(0);
  }
  to {
    transform: translateX(-2000px);
  }
}
.marqueeBox {
  /* 设置盒子是页面的50% */
  width: 50%;
  height: var(--line-height);
  /* 背景颜色以便调试 */
  background-color: darksalmon;
  /* 将盒子在页面内居中 */
  margin: 1px auto;
  display: flex;
  border-radius: 35px;
}
.marqueeWrapper {
  list-style-type: none;
  /* 默认将li横向排列 */
  display: flex;
  /* 每一个li内部内容不换行在本行内显示 */
  white-space: nowrap;
  /* overflow: hidden; */
  animation: moveMsg 10s infinite linear;
}
.marqueeItem{
    margin-right: 10px;
    line-height: var(--line-height);
}
</style>