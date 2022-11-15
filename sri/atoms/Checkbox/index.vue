<template>
  <label
    :for="id"
    :class="[$style[`checkbox`], !isBlock && $style[`checkbox--inline`]]"
  >
    <input
      :id="id"
      v-model="chk"
      type="checkbox"
      :name="name"
      :value="value"
      :disabled="disabled"
      :class="[$style[`checkbox__input`]]"
      @change="handleChange"
    />
    <span :class="[$style[`checkbox__icon`]]">
      <svgIcon
        name="check"
        :width="16"
        :height="16"
        :class="[$style[`icon__check`]]"
      />
    </span>
    <span
      :class="
        showLabel ? $style[`checkbox__label`] : $style[`checkbox__label--hide`]
      "
    >
      {{ label }}
    </span>
  </label>
</template>

<script>
export default {
  props: {
    context: {
      type: String,
      required: false,
      default: `inline`,
      validator(ctx) {
        return [`inline`, `block`].includes(ctx);
      },
    },
    label: {
      type: String,
      required: true,
      default: ``,
    },
    showLabel: {
      type: Boolean,
      required: false,
      default: true,
    },
    name: {
      type: String,
      required: false,
      default: ``,
    },
    value: {
      type: String,
      required: true,
      default: ``,
    },
    checked: {
      type: Boolean,
      required: false,
      default: false,
    },
    disabled: {
      type: Boolean,
      required: false,
      default: false,
    },
  },
  data() {
    return {
      id: `inputChk-${this.$_uid}`,
      chk: this.checked,
    };
  },
  computed: {
    isBlock() {
      return this.context === `block`;
    },
  },
  watch: {
    checked(val) {
      this.chk = val;
    },
  },
  methods: {
    handleChange(e) {
      this.chk = e.target.checked;
      /**
       * 선택 상태 변경 시 발생 <br>
       * `{ checked: Boolean, value: String }`
       *
       * @event change
       */
      this.$emit('change', {
        checked: this.chk,
        value: this.value,
      });
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
