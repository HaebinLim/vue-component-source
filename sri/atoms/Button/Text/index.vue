<template>
  <button
    :type="type"
    :disabled="disabled"
    :class="[
      $style[`button`],
      $style[`button--${appearance}`],
      { [$style[`button--${theme}`]]: theme === `dark` },
      { [$style['button--underline']]: useUnderLine },
      { [$style['button--lined']]: hasUnderLine },
    ]"
    @click="handleClick"
  >
    <template v-if="label">
      {{ label }}
    </template>
  </button>
</template>

<script>
export default {
  props: {
    type: {
      type: String,
      required: false,
      default: `button`,
      validator(type) {
        return [`button`, `reset`, `submit`].includes(type);
      },
    },
    disabled: {
      type: Boolean,
      required: false,
      default: false,
    },
    theme: {
      type: String,
      required: false,
      default: `bright`,
      validator(theme) {
        return [`bright`, `dark`].includes(theme);
      },
    },
    appearance: {
      type: String,
      required: true,
      default: ``,
      validator(type) {
        return [`basic`, `grey`].includes(type);
      },
    },
    label: {
      type: String,
      required: true,
      default: `레이블`,
      validator(label) {
        return label && !label.includes(`버튼`);
      },
    },
    useUnderLine: {
      type: Boolean,
      required: false,
      default: false,
    },
    hasUnderLine: {
      type: Boolean,
      required: false,
      default: false,
    },
    clickCallback: {
      type: [Function, null],
      required: false,
      default: null,
    },
  },
  methods: {
    handleClick() {
      /**
       * 클릭 시 발생 이벤트, no payload
       *
       * @event click
       */
      this.$emit(`click`);
      this.clickCallback && this.clickCallback();
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
