<template>
  <div class="calculator">
    <div class="result" style="grid-area: result">
      <div class="display">{{equation}}</div>
    </div>

    <!-- 操作部分 为gird-area起别名用于grid布局-->
    <button style="grid-area:ac" @click="del" @mousedown="clearDown" @mouseup="clearUp">AC</button>
    <button style="grid-area:plus-minus" @click="calculateToggle">±</button>
    <button style="grid-area:percent" @click="percentToggle">%</button>
    <button style="grid-area:divide" @click="append('÷')">÷</button>
    <button style="grid-area:multiply" @click="append('×')">×</button>
    <button style="grid-area:substract" @click="append('-')">-</button>
    <button style="grid-area:plus" @click="append('+')">+</button>
    <button style="grid-area:equal" @click="calculate">=</button>

    <!-- 数字部分 -->
    <button style="grid-area: number-1" @click="append(1)">1</button>
    <button style="grid-area: number-2" @click="append(2)">2</button>
    <button style="grid-area: number-3" @click="append(3)">3</button>
    <button style="grid-area: number-4" @click="append(4)">4</button>
    <button style="grid-area: number-5" @click="append(5)">5</button>
    <button style="grid-area: number-6" @click="append(6)">6</button>
    <button style="grid-area: number-7" @click="append(7)">7</button>
    <button style="grid-area: number-8" @click="append(8)">8</button>
    <button style="grid-area: number-9" @click="append(9)">9</button>
    <button style="grid-area: number-0" @click="append(0)">0</button>

    <button style="grid-area:dot" @click="append('.')">.</button>
  </div>
</template>

<script>
export default {
  name: "Calculator",
  data() {
    return {
      equation: "0",
      // 防止一组数字中间输入超过一个小数位
      isDecimalAdded: false,
      //判断是否已经点击加减乘除
      isOperatorAdded: false,
      // 判断计算器是否已输入数字
      isStarted: false,
      // 判断是否正在输入
      isEntering: false,
      // 判断是否已完成计算
      isFinished: false,
      timer: null
    };
  },
  methods: {
    // 判断加减乘除操作
    isOperator(char) {
      return ["+", "-", "×", "÷"].includes(char);
    },
    // 点击加减乘除、或小数点
    append(char) {
      //防止一开始输入的是运算符号
      if (this.equation === "0" && !this.isOperator(char)) {
        if (char === ".") {
          this.equation += "" + char;
          this.isDecimalAdded = true;
        } else {
          this.equation = "" + char;
        }
        this.isStarted = true;
        return;
      }
      // 若是数字字符
      if (!this.isOperator(char)) {
        // 判断是否已经有小数点
        if (char === "." && this.isDecimalAdded) {
          return;
        }
        if (char === ".") {
          this.isDecimalAdded = true;
        } else {
          this.isOperatorAdded = false;
        }
        // 判断是否运算完毕
        if (!this.isEntering && this.isFinished) {
          this.isEntering = true;
          this.isFinished = false;
          this.equation = "" + char;
          return;
        }
        this.equation += "" + char;
      }
      // 添加运算符号
      if (this.isOperator(char) && !this.isOperatorAdded) {
        this.equation += "" + char;
        this.isOperatorAdded = true;
        this.isDecimalAdded = false;
        this.isEntering = true;
      }
    },
    // 做最终运算
    calculate() {
      // 将X号和÷号替换成运算式版本
      let result = this.equation
        .replace(new RegExp("×", "g"), "*")
        .replace(new RegExp("÷", "g"), "/");
      // 结果限制为9位
      this.equation = parseFloat(
        eval(result)
          .toFixed(9)
          .toString()
      );
      // 转换科学计数法
      let res = eval(result);
      this.equation = (res < 1.0e9
        ? parseFloat(res.toFixed(9))
        : res.toExponential(3)
      ).toString();

      this.isDecimalAdded = false;
      this.isOperatorAdded = false;
      this.isEntering = false;
      this.isFinished = true;
    },
    // 切换正负号
    calculateToggle() {
      // 若刚输入运算符号，或者是未开始使用计算器
      if (!this.isStarted || this.isOperatorAdded) {
        return;
      }
      this.equation = this.equation + "* -1";
      this.calculate();
    },
    //切换百分比符号
    percentToggle() {
      if (!this.isStarted || this.isOperatorAdded) {
        return;
      }
      this.equation = this.equation + "* 0.01";
      this.calculate();
    },
    // 清除计算结果和数值
    del() {
      if (this.equation === "0" || this.equation.length === 1) {
        this.equation = "0";
        return;
      }
      this.equation = this.equation.slice(0, -1);
    },
    clearDown() {
      if (this.timer === null) {
        this.timer = setTimeout(() => {
          this.equation = "0";
          // 防止一组数字中间输入超过一个小数位
          this.isDecimalAdded = false;
          //判断是否已经点击加减乘除
          this.isOperatorAdded = false;
          // 判断计算器是否已输入数字
          this.isStarted = false;
          this.isEntering = false;
        }, 500);
      }
    },
    clearUp() {
      if (this.timer !== null) {
        clearTimeout(this.timer);
        this.timer = null;
      }
    }
  }
};
</script>

<style>
.calculator {
  --button-width: 80px;
  --button-height: 80px;
  display: grid;
  /* 将每个按钮按6行4列排布 */
  grid-template-areas:
    "result result result result"
    "ac plus-minus percent divide"
    "number-7 number-8 number-9 multiply"
    "number-4 number-5 number-6 substract"
    "number-1 number-2 number-3 plus"
    "number-0 number-0 dot equal";
  grid-template-columns: repeat(4, var(--button-width));
  grid-template-rows: repeat(6, var(--button-height));
  box-shadow: -8px -8px 16px -10px rgba(255, 255, 255, 1),
    8px 8px 16px -10px rgba(0, 0, 0, 0.15);
  padding: 24px;
  border-radius: 20px;
}
.calculator button {
  display: block;
  margin: 8px;
  padding: 0;
  border: 0;
  color: #999;
  font: 24px Helvetica normal;
  outline: none;
  border-radius: calc(var(--button-height) / 2);
  background: linear-gradient(
    135deg,
    rgba(230, 230, 230, 1) 0%,
    rgba(246, 246, 246, 1) 100%
  );
  /* 利用box-shadow设计拟态 分别设置左上和右下的阴影*/
  box-shadow: -4px -4px 10px -8px rgba(255, 255, 255, 1),
    4px 4px 10px -8px rgba(0, 0, 0, 0.3);
}
.calculator button:active {
  /* 加入内部阴影  */
  box-shadow: -4px -4px 10px -8px rgba(255, 255, 255, 1) inset,
    4px 4px 10px -8px rgba(0, 0, 0, 0.3) inset;
}
.result {
  white-space: nowrap;
  overflow: hidden;
  text-align: right;
  font: 48px Helvetica;
  line-height: var(--button-width);
  padding: 0 20px;
  color: #666;
}
/* 解决显示结果溢出问题 */
.result .display {
  position: relative;
  float: right;
}
</style>