<template>
  <div class="main">
    <div class="scroll-container">
      <div
        v-for="index in 24"
        :key="index"
        class="scroll-item"
        :style="styleFormatter(index - 1)"
        ref="scrollItem"
      >
        {{ index }}
      </div>
    </div>
    <div class="controller">
      <button class="btn" @click="toggleClick">
        {{ isScrolling ? "暂停滚动" : "开始滚动" }}
      </button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      colorList: [],

      // 每个元素的宽度
      width: 200,

      // 滚动速度
      speed: 300,

      isScrolling: false,
    };
  },
  mounted() {
    this.initListener();
    this.toggleClick();
  },
  methods: {
    toggleClick() {
      if (this.isScrolling) {
        this.isScrolling = false;
        this.pause();
      } else {
        this.isScrolling = true;
        this.scroll();
      }
    },

    // 根据索引计算当前元素所在位置
    styleFormatter(index) {
      return {
        backgroundColor: this.colorList[index],
        left: `${index * this.width}px`,
      };
    },

    // 控制滚动开始
    scroll() {
      const elementList = this.$refs.scrollItem;
      elementList.forEach((ele) => {
        ele.style.transitionDuration = `${
          (ele.offsetLeft + this.width) / this.speed
        }s`;
        ele.style.left = `-${this.width}px`;
      });
    },

    // 暂停滚动
    pause() {
      const elementList = this.$refs.scrollItem;
      elementList.forEach((ele) => {
        const styles = getComputedStyle(ele, null);
        console.log(styles.left);
        ele.style.transitionDuration = "0s";
        ele.style.left = `${styles.left}`;
      });
    },

    // 初始化listener，监听滚动动画
    // 当元素完全滚动至显示范围外时，则将该元素插入队尾
    initListener() {
      for (let i = 0; i <= 24; i++) {
        this.colorList.push(i);
      }

      const elementList = this.$refs.scrollItem;

      elementList.forEach((ele) => {
        ele.addEventListener("transitionend", () => {
          const lastestElementPosition = this.lastestElementPosition();
          ele.style.transitionDuration = `0s`;
          ele.style.left = `${lastestElementPosition + this.width}px`;

          this.$nextTick(() => {
            ele.style.transitionDuration = `${
              (ele.offsetLeft + 200) / this.speed
            }s`;
            ele.style.left = `-${this.width}px`;
          });
        });
      });
    },

    lastestElementPosition() {
      const elementList = this.$refs.scrollItem;
      return Math.max(
        ...elementList.map((ele) => {
          const styles = getComputedStyle(ele, null);
          return parseFloat(styles.left);
        })
      );
    },
  },
};
</script>

<style scoped>
.scroll-container {
  width: 100%;
  height: calc(100% - 50px);
  display: flex;
  flex-direction: row;
  position: relative;
  overflow: hidden;
}

.scroll-item {
  display: flex;
  flex-shrink: 0;
  width: 200px;
  height: 100%;
  position: absolute;
  transition-property: left;
  transition-timing-function: linear;
  background: #3f51b5;
  border: 1px solid;
}

.btn {
  margin-top: 20px;
  height: 30px;
  line-height: 30px;
}
</style>
