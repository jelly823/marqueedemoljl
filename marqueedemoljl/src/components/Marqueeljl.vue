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
      <button v-if="roll" @click="speedUp">动态加速</button>
      <button v-if="roll" @click="speedDown">动态减速</button>
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
      translateXljl: 0,
      startPos: 0,
      myspeed: 100,
      minSpeed: 100,
      speedStep: 100,
      roll: false,
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

    // 关键帧【滚动动画】规则
    createAni(name, animationIterationCount) {
      return `
          @keyframes ${name} { 
              from {transform: translateX(${this.startPos}px);}
              to {transform: translateX(-${this.itemWidth}px);}
          }
          .${name}{
            animation: ${name} ${this.duration}s ${animationIterationCount} linear;
          }
        `;
    },

    // 操作ul样式，返回样式类名aniName
    insertMyRule(aniName, animationIterationCount) {
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

        // 生成动画并插入style
        if (!this.myStyle) {
          this.myStyle = document.createElement("style");
          this.myStyle.setAttribute("type", "text/css");
          document.getElementsByTagName("head")[0].appendChild(this.myStyle);
        }
        this.myStyle.innerHTML = this.createAni(
          aniName,
          animationIterationCount
        );
        return aniName;
      } else {
        console.log("【弹幕宽度 < 盒子宽度】 不可以滚动哦～");
        this.myspeed = 0;
        return "";
      }
    },
    // 更改动画，所有涉及到修改动画的都需要使tempchange动画执行完，重新插入更新的defalutAni动画
    changeAni(tempchange) {
      this.speeding = true;
      this.initData();
      this.aniName = this.insertMyRule(tempchange, 1);
      this.$refs.marqueeUl.onanimationend = () => {
        this.speeding = false;
        this.startPos = 0;
        this.aniName = this.insertMyRule("defalutAni", "infinite");
      };
    },

    // 加速
    speedUp() {
      this.myspeed += this.speedStep;
      if (this.speeding === true) {
        this.aniName.substr(0, 2) == "or"
          ? this.changeAni("faster")
          : this.changeAni("orfaster");
      } else {
        this.changeAni("faster");
      }
    },
    // 减速
    speedDown() {
      if (this.myspeed > this.minSpeed) {
        this.myspeed -= this.speedStep;
        if (this.speeding === true) {
          this.aniName.substr(0, 2) == "or"
            ? this.changeAni("slower")
            : this.changeAni("orslower");
        } else {
          this.changeAni("slower");
        }
      }
    },
    // 添加弹幕
    addMsg(e) {
      // eslint-disable-next-line vue/no-mutating-props
      this.msg.push(e.target.value);
      e.target.value = "";
      this.$nextTick(() => {
        if (this.speeding === true) {
          this.aniName.substr(0, 2) == "or"
            ? this.changeAni("addMsg")
            : this.changeAni("oraddMsg");
        } else {
          this.changeAni("addMsg");
        }
      });
    },
  },
  mounted() {
    this.initData();
    this.aniName = this.insertMyRule("defalutAni", "infinite");

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