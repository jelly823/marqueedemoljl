<template>
  <div class="marqueeBox" ref="marqueeBox">
    <ul class="marqueeWrapper" ref="marqueeWrapper">
      <li class="marqueeItem" v-for="item in msg" v-bind:key="item.id">
        {{ item }}
      </li>
      <li :style="{width:`${wrapperWidth}px`,color:'red'}">
        <span class="marqueeItem"  v-for="item in msg" v-bind:key="item.id">{{ item }}</span>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: "Marqueeljl",
  data() {
    return{
        wrapperWidth:{
            default:0,
            type: Number,
        },
        itemWidth:{
            default:0,
            type: Number,
        }
    };
  },
  props: {
    msg: {
      default: () => [],
      type: Array,
    },
  },
  methods:{
      test(){
          const { marqueeWrapper, marqueeBox } =this.$refs;// vue 操作dom
          // console.log(window.getComputedStyle(marqueeBox).width);
          this.wrapperWidth = parseFloat(window.getComputedStyle(marqueeBox).width);
          console.log(window.getComputedStyle(marqueeWrapper).width);
          this.itemWidth = parseFloat(window.getComputedStyle(marqueeWrapper).width);
      }
  },
  mounted(){
      this.test();
  }
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
    transform: translateX(-2182.31px);
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
  overflow: hidden;
}
.marqueeWrapper {
  list-style-type: none;
  /* 默认将li横向排列 */
  display: flex;
  /* 每一个li内部内容不换行在本行内显示 */
  white-space: nowrap;
  animation: moveMsg 20s infinite linear;
}
.marqueeItem{
    margin-right: 10px;
    line-height: var(--line-height);
}
</style>