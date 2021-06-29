<template>
  <div>
    <div class="marqueeBox" ref="marqueeBox">
      <ul class="marqueeUl" :class="aniName" ref="marqueeUl">
        <li ref="marqueeLi">
          <div class="marqueeItem" v-for="item in msg" v-bind:key="item.id">
            {{ item }}
          </div>
        </li>
        <li v-show="roll" ref="marqueeLi2" :style="{ width: `${boxWidth}px` }">
          <div class="marqueeItem" v-for="item in msg" v-bind:key="item.id">
            {{ item }}
          </div>
        </li>
      </ul>
    </div>
    <div class="funArea">
      <button v-if="roll" @click="speedUpAlways">动态加速</button>
      <button v-if="roll" @click="speedDownAlways">动态减速</button>
      <div>
        <label for="addMsg">添加弹幕:</label>
        <input
          id="addMsg"
          type="text"
          placeholder="来吐槽吧..."
          @keyup.enter="addMsg"
        />
      </div>
    </div>
    <div>
      当前动画速度speed：{{ this.myspeed }} <br />
      当前动画持续时间duration：{{ this.duration }}<br />
      当前盒子宽度boxWidth：{{ this.boxWidth }}<br />
      当前一个弹幕总宽度itemWidth：{{ this.itemWidth }}<br />
      marqueeLi2宽度：{{ this.item2 }}<br />
      当前ul的translateX值：{{ this.translateXljl }}<br />
    </div>
  </div>
</template>

<script>
export default {
  name: "Marqueeljl",
  data() {
    return {
      boxWidth: 0,
      itemWidth: 0,
      item2: 0,
      myspeed: 100,
      minSpeed: 100,
      speedStep: 100,
      roll: false,
      translateXljl: 0,
      startPos: 0,
      aniName: "",
      myStyle: null,
      speeding: false,
    };
  },
  props: {
    msg: {
      default: () => [],
      type: Array,
    },
  },
  computed: {
    duration() {
      return this.myspeed === 0
        ? 0
        : (this.itemWidth + this.startPos) / this.myspeed; // 初始化【计算动画持续时间 duration】
    },
  },
  methods: {
    // 初始化获取【挂载元素信息】
    initData() {
      const { marqueeBox, marqueeLi, marqueeUl, marqueeLi2 } = this.$refs;
      this.boxWidth = parseFloat(window.getComputedStyle(marqueeBox).width); // 弹幕盒子实际宽度
      this.itemWidth = parseFloat(window.getComputedStyle(marqueeLi).width); // 一个li总长度
      this.$nextTick(() => {
        this.translateXljl = parseFloat(
          window.getComputedStyle(marqueeUl).transform.split(",")[4]
        );
        this.item2 = parseFloat(window.getComputedStyle(marqueeLi2).width); // marqueeLi2依赖boxWidth大小
      });
    },

    // 操作ul样式，以及对应关键帧【滚动动画】规则
    insertMyRule(aniName, animationIterationCount = "infinite") {
      //如果 【弹幕宽度 > 盒子宽度】 才开始滚动
      if (this.itemWidth > this.boxWidth) {
        console.log("【弹幕宽度 > 盒子宽度】 可以滚动啦！");
        // 设置滚动的第二个li 以及所有加减速按钮
        this.roll = true;

        // 先计算动画开始位置startPos
        this.startPos =
          parseFloat(
            window
              .getComputedStyle(this.$refs.marqueeUl)
              .transform.split(",")[4]
          ) || 0;

        // 设置动画
        const myAnimation = `
          @keyframes ${aniName} { 
              from {transform: translateX(${this.startPos}px);}
              to {transform: translateX(-${this.itemWidth}px);}
          }
          .${aniName}{
            animation: ${aniName} ${this.duration}s ${animationIterationCount} linear;
          }
        `;
        // 生成并插入style
        if (!this.myStyle) {
          this.myStyle = document.createElement("style");
          this.myStyle.setAttribute("type", "text/css");
          document.getElementsByTagName("head")[0].appendChild(this.myStyle);
        }
        this.myStyle.innerHTML = myAnimation;
        // 将样式类名设置给ul
        this.aniName = aniName;
      } else {
        console.log("【弹幕宽度 < 盒子宽度】 不可以滚动哦～");
        this.myspeed = 0;
      }
    },
    // 更改动画
    changeAni() {
      this.speeding = true;
      this.initData();
      this.insertMyRule("temp", 1);
      this.$refs.marqueeUl.onanimationend = () => {
        this.speeding = false;
        this.startPos = 0;
        this.insertMyRule("defalutAni");
      };
    },

    // 加速
    speedUpAlways() {
      if (this.speeding === false) {
        this.myspeed += this.speedStep;
        this.changeAni();
      }
    },
    // 减速
    speedDownAlways() {
      if (this.speeding === false && this.myspeed > this.minSpeed) {
        this.myspeed -= this.speedStep;
        this.changeAni();
      }
    },
    // 添加弹幕
    addMsg(e) {
      if (this.speeding === false) {
        // eslint-disable-next-line vue/no-mutating-props
        this.msg.push(e.target.value);
        e.target.value = "";
        this.$nextTick(() => {
          this.changeAni();
        });
      }
    },
  },
  mounted() {
    this.initData();
    this.insertMyRule("defaultAni");

    // 实时获取并展示translateXljl(可以删除)
    setInterval(() => {
      this.translateXljl = parseFloat(
        window.getComputedStyle(this.$refs.marqueeUl).transform.split(",")[4]
      );
    }, 500);
  },
  beforeUnmount() {
    document.getElementsByTagName("head")[0].removeChild(this.myStyle);
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
.marqueeBox {
  width: 50%;
  height: var(--line-height);
  background-color: darksalmon;
  margin: 1px auto;
  display: flex;
  border-radius: 35px;
  overflow: hidden;
}
.marqueeUl {
  list-style-type: none;
  display: flex;
  white-space: nowrap;
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