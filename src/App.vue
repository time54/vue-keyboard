<template>
  <div id="app">
    <div class="input-field">
      <div class="price" @click.stop="showKeyboard" data-type="P">
        <div class="common">买入价格:</div>
        <div class="common other">
          {{price}}
          <img
            class="pCursor"
            v-if="isShowKeyboard && boxType=='P'"
            src="https://s.thsi.cn/js/m/kh/mncg/weChat/images/cursor.gif"
          />
        </div>
      </div>
      <div class="num" @click.stop="showKeyboard" data-type="N">
        <div class="common">买入数量:</div>
        <div class="common other">
          {{num}}
          <img
            class="pCursor"
            v-if="isShowKeyboard && boxType=='N'"
            src="https://s.thsi.cn/js/m/kh/mncg/weChat/images/cursor.gif"
          />
        </div>
      </div>
    </div>
    <num-keyboard
      v-if="isShowKeyboard"
      :boxType="boxType"
      :initNum="initNum"
      @changeNum="changeNum"
      @hideKeyboard="hideKeyboard"
    />
  </div>
</template>
money
<script>
import NumKeyboard from "./components/NumKeyboard.vue";

export default {
  name: "App",
  components: {
    NumKeyboard,
  },
  data() {
    return {
      isShowKeyboard: false, // 是否显示数字键盘
      boxType: "",
      initNum: "",
      price: "",
      num: "",
    };
  },
  methods: {
    showKeyboard(e) {
      this.boxType = e.currentTarget.dataset.type;
      this.boxType == "P"
        ? (this.initNum = this.price)
        : (this.initNum = this.num);
      this.isShowKeyboard = true;
    },
    changeNum(result) {
      if (this.boxType == "P") {
        this.price = result;
      } else {
        this.num = result;
      }
    },
    hideKeyboard() {
      this.isShowKeyboard = false;
    }

  },
};
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.input-field {
  width: 100%;
  height: 2rem;
  font-size: 20px;
  .price,
  .num {
    display: flex;
    align-items: center;
    position: relative;
    .common {
      height: 1rem;
      display: flex;
      align-items: center;
      padding-left: 0.2rem;
    }
    .other {
      width: 50%;
      background-color: cornsilk;
    }
    .pCursor {
      width: 2px;
      height: 65%;
      margin-left: 0.1rem;
    }
  }
  .num {
    margin-top: 1rem;
  }
}
</style>
