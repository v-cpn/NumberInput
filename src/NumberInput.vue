<template>
  <div class="box">
    <span v-if="disabled">{{
      showValue || showValue === 0 ? showValue : '-'
    }}</span>
    <input
      v-else
      class="money"
      :class="textAlign"
      @input="handleInput"
      @blur="handleBlur"
      @focus="handleFocus"
      @keyup.enter="e => e.target.blur()"
      v-model="showValue"
      :placeholder="placeholder"
      :style="'padding-right:' + wid + 'px;'"
    />
    <span
      v-if="showValue || showValue === 0 || !disabled"
      :class="{ disabled }"
      class="unit"
      ref="unit"
      >{{ ' ' + unit }}</span
    >
  </div>
</template>

<script>
export default {
  props: {
    value: [String, Number], // 真实值，number 类型
    textAlign: { type: String, default: 'right' },
    disabled: Boolean,
    unit: { type: String, default: '' },
    max: Number,
    min: Number,
    placeholder: String,
    decimal: { type: Number, default: 2 },
    formatter: Function,
  },
  data() {
    return {
      showValue: '', // 展示值，string 类型
      selfChange: false, // 是否内部修改
      wid: '', // 单位宽度
    }
  },
  mounted() {},
  watch: {
    unit: {
      immediate: true,
      handler() {
        this.$nextTick(() => {
          if (this.unit) this.wid = this.unit.length * 10 + 25
        })
      },
    },
    value: {
      immediate: true,
      handler(newValue, oldValue) {
        if (this.selfChange) {
          // 自修改时跳过计算
          this.selfChange = false
        } else {
          if (newValue === 0) this.showValue = 0
          let val = newValue
          if (this.formatter) val = this.formatter(newValue)
          this.showValue = newValue && val
        }
      },
    },
  },
  methods: {
    handleBlur(e) {
      const exportValue = this.showValue && Number(this.showValue)
      this.selfChange = true
      // formatter
      if (this.formatter) this.showValue = this.formatter(this.showValue)
      this.$emit('input', exportValue)
      this.$emit('blur', exportValue)
      this.$emit('change', exportValue)
    },
    handleFocus() {
      this.showValue = this.value
    },
    handleInput(e) {
      const decimalReg = new RegExp(`\\d+(\\.\\d{0,${this.decimal}})?`)
      // 删除所有非数字字符，并控制小数点位数
      let match = e.target.value.replace(/[^\d|^.]/g, '').match(decimalReg)
      if (!match) this.showValue = ''
      else {
        match = match[0]
        if (this.max && Number(match) > this.max) {
          this.showValue = this.showValue.slice(0, -1)
        } else if (this.min && Number(match) < this.min) {
          this.showValue = this.showValue.slice(0, -1)
        } else {
          this.showValue = match
        }
      }
      this.selfChange = true
    },
  },
}
</script>

<style scoped>
.box {
  position: relative;
}
.money {
  box-sizing: border-box;
  width: 100%;
  text-align: right;
  border: 1px solid #dcdfe6;
  padding: 4px 28px 4px 10px;
  border-radius: 4px;
  height: 40px;
  line-height: 40px;
}
.right {
  text-align: right;
}
.left {
  text-align: left;
}
.unit {
  position: absolute;
  right: 15px;
  line-height: 40px;
}
.unit.disabled {
  position: relative !important;
  right: 0 !important;
  line-height: 40px;
  padding: 0;
}
input::-webkit-input-placeholder {
  /* WebKit browsers 适配谷歌 */
  color: #bdcada;
}
input:-moz-placeholder {
  /* Mozilla Firefox 4 to 18 适配火狐 */
  color: #bdcada;
}
input::-moz-placeholder {
  /* Mozilla Firefox 19+ 适配火狐 */
  color: #bdcada;
}
input:-ms-input-placeholder {
  /* Internet Explorer 10+  适配ie*/
  color: #bdcada;
}
</style>
