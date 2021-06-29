<template>
  <Marqueeljl
    v-if="msg && msg.length > 0"
    :msg="msg"
  ></Marqueeljl>
</template>

<script>
import Marqueeljl from "./components/Marqueeljl.vue";

export default {
  name: "App",
  components: {
    Marqueeljl,
  },
  data() {
    return {
      msg: [
        "msg1这是我的走马灯",
        "msg2写代码",
        // "msg3吃饭",
        // "msg4睡觉",
        // 'msg5打豆豆',
        // 'msg6天气好',
        // 'msg7天气大好',
        // 'msg8天气特别好',
        // 'msg9天气绝佳',
        // 'msg10阿哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈',
        // 'msg11双十一',
        // 'msg12双十二',
        "msg13三长一短选一短",
        "msg14三短一长选一长",
        "msg15最后一条消息。。。。",
      ],
    };
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
