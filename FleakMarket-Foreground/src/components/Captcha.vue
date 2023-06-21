<template>
  <!-- S 验证码组件 -->
  <div class="s-canvas">
    <canvas ref="canvas" :width="contentWidth" :height="contentHeight"></canvas>
  </div>
  <!-- E 验证码组件 -->
</template>

<script>
export default {
  name: "imageCode",
  props: {
    change: {
      // 刷新验证码使用
      type: Boolean,
      default: false
    },
    contentWidth: {
      // 验证码图片宽
      type: Number,
      default: 130
    },
    contentHeight: {
      // 验证码图片高
      type: Number,
      default: 47
    }
  },
  data() {
    return {
      // 默认值
      identifyCode: "", // 验证码值，未加密的
      identifyCodes:
        "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ1234567890", // 生成验证码的元素，可以加入字母
      fontSizeMin: 30, // 图片上验证文字的最小值
      fontSizeMax: 40, // 图片上验证文字的最大值
      backgroundColorMin: 180, // 图片背景色值最小
      backgroundColorMax: 240, // 图片背景色值最大
      colorMin: 50, // 文字色值最小
      colorMax: 160, // 文字色值最大
      lineColorMin: 40, // 干扰线色值最小
      lineColorMax: 120, // 干扰线色值最大
      dotColorMin: 0, // 干扰点色值最小
      dotColorMax: 255, // 干扰点色值最大
      lineSum: 4, // 干扰线数量
      dotSum: 40 // 干扰点数量
    };
  },
  mounted() {
    this.makeCode();
  },
  watch: {
    change() {
      this.makeCode();
    }
  },
  methods: {
    // 生成一个随机数
    randomNum(min, max) {
      return Math.floor(Math.random() * (max - min) + min);
    },
    // 生成一个随机的颜色
    randomColor(min, max) {
      let r = this.randomNum(min, max);
      let g = this.randomNum(min, max);
      let b = this.randomNum(min, max);
      return "rgb(" + r + "," + g + "," + b + ")";
    },
    // 创建图形
    drawPic() {
      let canvas = this.$refs.canvas;
      let ctx = canvas.getContext("2d");
      ctx.textBaseline = "bottom";
      // 绘制背景
      ctx.fillStyle = this.randomColor(
        this.backgroundColorMin,
        this.backgroundColorMax
      );
      ctx.fillRect(0, 0, this.contentWidth, this.contentHeight);
      // 绘制文字
      for (let i = 0; i < this.identifyCode.length; i++) {
        this.drawText(ctx, this.identifyCode[i], i);
      }
      this.drawLine(ctx);
      this.drawDot(ctx);
    },
    // 绘制文字
    drawText(ctx, txt, i) {
      ctx.fillStyle = this.randomColor(this.colorMin, this.colorMax);
      ctx.font =
        this.randomNum(this.fontSizeMin, this.fontSizeMax) + "px SimHei";
      let x = (i + 1) * (this.contentWidth / (this.identifyCode.length + 1));
      let y = this.randomNum(this.fontSizeMax, this.contentHeight - 5);
      var deg = this.randomNum(-45, 45);
      // 修改坐标原点和旋转角度
      ctx.translate(x, y);
      ctx.rotate((deg * Math.PI) / 180);
      ctx.fillText(txt, 0, 0);
      // 恢复坐标原点和旋转角度
      ctx.rotate((-deg * Math.PI) / 180);
      ctx.translate(-x, -y);
    },
    // 绘制干扰线
    drawLine(ctx) {
      for (let i = 0; i < 4; i++) {
        ctx.strokeStyle = this.randomColor(
          this.lineColorMin,
          this.lineColorMax
        );
        ctx.beginPath();
        ctx.moveTo(
          this.randomNum(0, this.contentWidth),
          this.randomNum(0, this.contentHeight)
        );
        ctx.lineTo(
          this.randomNum(0, this.contentWidth),
          this.randomNum(0, this.contentHeight)
        );
        ctx.stroke();
      }
    },
    // 绘制干扰点
    drawDot(ctx) {
      for (let i = 0; i < 60; i++) {
        ctx.fillStyle = this.randomColor(0, 255);
        ctx.beginPath();
        ctx.arc(
          this.randomNum(0, this.contentWidth),
          this.randomNum(0, this.contentHeight),
          1,
          0,
          2 * Math.PI
        );
        ctx.fill();
      }
    },
    // 生成图片
    makeCode() {
      this.identifyCode = "";
      for (let i = 0; i < 4; i++) {
        this.identifyCode += this.identifyCodes[
          this.randomNum(0, this.identifyCodes.length)
        ];
      }

      // 绘制图片
      this.drawPic();
      // 返回加密后的图片验证码值
      this.$emit("getCode", this.identifyCode.toUpperCase());
    }
  }
};
</script>

<style></style>
