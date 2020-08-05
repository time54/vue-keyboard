<template>
  <div class="NumKeyboard" @click.stop="_handleKeyPress">
    <div class="key-left">
      <div class="key-row">
        <div class="key-cell other" data-num="ALL" v-show="boxType == 'N'">全仓</div>
        <div class="key-cell" data-num="1">1</div>
        <div class="key-cell" data-num="2">2</div>
        <div class="key-cell" data-num="3">3</div>
      </div>
      <div class="key-row">
        <div class="key-cell other" data-num="HALF" v-show="boxType == 'N'">半仓</div>
        <div class="key-cell" data-num="4">4</div>
        <div class="key-cell" data-num="5">5</div>
        <div class="key-cell" data-num="6">6</div>
      </div>
      <div class="key-row">
        <div class="key-cell other" data-num="THIRD" v-show="boxType == 'N'">1/3仓</div>
        <div class="key-cell" data-num="7">7</div>
        <div class="key-cell" data-num="8">8</div>
        <div class="key-cell" data-num="9">9</div>
      </div>
      <div class="key-row">
        <div class="key-cell other" data-num="FOUR" v-show="boxType == 'N'">1/4仓</div>
        <div class="key-cell" data-num="." v-show="boxType == 'P'">.</div>
        <div class="key-cell" data-num="000" v-show="boxType == 'N'">000</div>
        <div class="key-cell" data-num="0">0</div>
        <div class="key-cell hCenter" data-num="H">
          <img
            class="hideKB"
            data-num="H"
            src="https://s.thsi.cn/js/m/kh/mncg/weChat/images/hide.png"
          />
        </div>
      </div>
    </div>
    <div class="key-right">
      <div class="key-confirm" data-num="D">
        <img
          class="delateNum"
          data-num="D"
          src="https://s.thsi.cn/js/m/kh/mncg/weChat/images/delate.png"
        />
      </div>
      <div class="key-confirm" data-num="S">{{busDirection == 1 ? '买入': '卖出'}}</div>
    </div>
  </div>
</template>

<script>
export default {
  name: "NumKeyboard",
  props: {
    boxType: {
      // 输入框类型【P 价格 N 数量】
      type: String,
      default: "",
    },
    initNum: {
      // 输入框初始值
      type: String,
      default: "",
    },
    busDirection: {
      // 买入&卖出方向【1 买入  2 卖出】
      type: Number,
      default: 1,
    },
  },
  data() {
    return {
      money: "",
    };
  },
  methods: {
    _handleKeyPress(e) {
      let num = e.target.dataset.num;
      /*不同按键处理逻辑
       * -1 代表无效按键，直接返回
       */
      if (num == -1) return false;
      switch (String(num)) {
        case ".": // 小数点
          this._handleDecimalPoint();
          break;

        case "D": // 删除键
          this._handleDeleteKey();
          break;

        case "C": // 清空键
          this._handleClearKey();
          break;

        case "H": // 隐藏键
          this._handleHideKey();
          break;

        case "S": // 确认键
          this._handleConfirmKey();
          break;

        case "ALL": // 全仓
          this.changeHold(1);
          break;

        case "HALF": // 半仓
          this.changeHold(0.5);
          break;

        case "THIRD": // 1/3仓
          this.changeHold(0.33);
          break;

        case "FOUR": // 1/4仓
          this.changeHold(0.25);
          break;

        default:
          // 数字键
          this._handleNumberKey(num);
          break;
      }
    },
    //处理小数点函数
    _handleDecimalPoint() {
      //如果包含小数点，直接返回
      if (this.money.indexOf(".") > -1) return false;
      //如果小数点是第一位，补0
      if (!this.money.length) this.money = "0.";
      //如果不是，添加一个小数点
      else this.money = this.money + ".";
      this.changeNum();
    },
    //处理删除键
    _handleDeleteKey() {
      let S = this.money;
      //如果没有输入，直接返回
      if (!S.length) return false;
      //否则删除最后一个
      this.money = S.substring(0, S.length - 1);
      this.changeNum();
    },
    //处理清空键
    _handleClearKey() {
      this.money = "";
    },
    //处理数字
    _handleNumberKey(num) {
      let S = this.money;
      //如果有小数点且小数点位数小于2
      if (S.indexOf(".") > -1 && S.substring(S.indexOf(".") + 1).length < 2)
        this.money = S + num;

      //没有小数点
      if (!(S.indexOf(".") > -1)) {
        //如果第一位是0，只能输入小数点
        if (num == 0 && S.length == 0) this.money = "0.";
        else {
          if (S.length && Number(S.charAt(0)) === 0) return;
          this.money = S + num;
        }
      }
      this.changeNum();
    },

    // 处理隐藏键盘
    _handleHideKey() {
      this.$emit("hideKeyboard");
    },
    //提交
    _handleConfirmKey() {
      let S = this.money;
      //未输入
      if (!S.length) {
        alert("您目前未输入!");
        return false;
      }
      //将 8. 这种转换成 8.00
      if (S.indexOf(".") > -1 && S.indexOf(".") == S.length - 1)
        S = Number(S.substring(0, S.length - 1)).toFixed(2);
      //保留两位
      S = Number(S).toFixed(2);
      this.$emit("confirmEvent", S);
    },
    changeNum() {
      this.$emit("changeNum", this.money);
    },
  },
  watch: {
    boxType() {
      console.log("this.initNum" + this.initNum);
      this.money = this.initNum;
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
.NumKeyboard {
  width: 100%;
  height: 6rem;
  display: flex;
  position: fixed;
  bottom: 0;
  left: 0;
  background-color: #fff;
}

.key-left {
  width: 75%;
  height: 100%;
  display: flex;
  flex-direction: column;
  .key-row {
    display: flex;
    .key-cell {
      flex: 1;
      height: 1.5rem;
      line-height: 1.5rem;
      text-align: center;
      border-top: 1px solid #d5d5d6;
      border-right: 1px solid #d5d5d6;
      font-size: 23px;
      color: #5c5c5c;
    }
    .other {
      font-size: 14px;
    }
    .hCenter {
      display: flex;
      justify-content: center;
      align-items: center;
      .hideKB {
        width: 0.67rem;
        height: 0.52rem;
      }
    }
  }
}

.key-right {
  width: 25%;
  height: 100%;
  display: flex;
  flex-direction: column;
  .key-confirm {
    width: 100%;
    height: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    border-top: 1px solid #d5d5d6;
    font-size: 14px;
    .delateNum {
      width: 0.6rem;
      height: 0.37rem;
    }
  }
}
</style>
