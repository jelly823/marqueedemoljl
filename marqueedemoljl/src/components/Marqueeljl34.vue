<template>
  <div>
    <div class="marqueeBox" ref="marqueeBox">
      <ul
        class="marqueeWrapper"
        ref="marqueeWrapper"
        :style="{ animationDuration: `${duration}s` }"
      >
        <li class="marqueeItem" v-for="item in msg" v-bind:key="item.id">
          {{ item }}
        </li>
        <li v-if="roll" :style="{ width: `${wrapperWidth}px`,}">
          <div class="marqueeItem" v-for="item in msg" v-bind:key="item.id">
            {{ item }}
          </div>
        </li>
      </ul>
    </div>
    <div class="funArea">
      <button v-if="roll" @click="speedUp">动态加速</button>
      <div>
        <label for="addMsg">添加弹幕:</label>
        <input id="addMsg" type="text" placeholder="来吐槽吧..." @keyup.enter="addMsg" />
      </div>
    </div>
    <div>
      当前动画速度speed：{{this.speed}} <br />
      当前动画持续时间duration：{{this.duration}}
    </div>
  </div>
</template>

<script>
export default {
  name: "Marqueeljl",
  data() {
    return {
      wrapperWidth: 0,
      itemWidth: 0,
      myspeed: 100,
      roll: false,
      timer: null,
      curVal: '',
    };
  },
  props: {
    msg: {
      default: () => [],
      type: Array,
    },
  },
  computed: {
    speed() {
      return this.myspeed;
    },
    duration(){
      return this.itemWidth / this.speed;
    },
  },
  methods: {
    // 初始化获取【宽度信息】
    init() {
      const { marqueeWrapper, marqueeBox } = this.$refs;
      this.wrapperWidth = parseFloat(window.getComputedStyle(marqueeBox).width); // 弹幕盒子实际宽度
      this.itemWidth = parseFloat(
        window.getComputedStyle(marqueeWrapper).width
      ); // 完整所有弹幕实际宽度
    },

    // 初始化【计算动画持续时间 duration】
    initDuration(){
      this.duration = this.itemWidth / this.speed; 
    },

    // 用于操作样式关键帧，插入【滚动动画】规则
    insertMyRule() {
      //如果 【弹幕宽度 > 展示的盒子宽度】 才开始滚动
      if (this.itemWidth < this.wrapperWidth) {
        this.roll = true;
        const styleSheets = document.styleSheets[0];
        styleSheets.deleteRule(0);
        styleSheets.insertRule(
          `@keyframes moveMsg {from {transform: translateX(0);} to {transform: translateX(-${this.itemWidth}px);}}`,
          0
        );
      }else{
        this.myspeed = 0;
        this.duration = 0;
      }
    },

    // 用于控制动态加速
    speedUp() {
      this.myspeed += 100;
      this.initDuration();
      console.log("now speed:", this.speed);
      if (!this.timer) {
        setTimeout(() => {
          this.myspeed = 100;
          this.initDuration();
          console.log("now speed:", this.speed);
        }, 2000);
      }else{
        clearTimeout(this.timer);
      }
    },

    // 用于添加弹幕
    addMsg(e){
      // eslint-disable-next-line vue/no-mutating-props
      this.msg.push(e.target.value);
      e.target.value = '';
      this.init();
      this.insertMyRule();
    }
  },
  mounted() {
    this.init();
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
  overflow: hidden;
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
  display: inline-block;
}

.funArea {
  margin: 30px auto;
  width: 50%;
  display: flex;
  justify-content: space-around;
}
</style>