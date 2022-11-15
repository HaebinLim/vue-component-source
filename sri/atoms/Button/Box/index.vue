<template>
  <button
    ref="button"
    :type="type"
    :disabled="isDisabled"
    :aria-label="
      isHiddenLabel && (hasAlarm ? `${label} ${newAlarmMsg}` : label)
    "
    :aria-pressed="pressed"
    :class="[
      $style[`button`],
      { [$style[`button--block`]]: isBlock },
      { [$style[`button--inactive`]]: inactive },
      $style[`button--${size}`],
      $style[`button--${appearance}`],
      { [$style[`button--${appearance}--${icon}`]]: isCircle },
      { [$style[`button--${colorType}`]]: !isArchiveIcon && colorType },
      $style[`button--${theme}`],
    ]"
    @click="handleClick"
  >
    <svgIcon
      v-if="icon"
      :name="icon"
      :width="svgSize"
      :height="svgSize"
      :aria-hidden="isIconType || null"
      :class="[
        { [$style[`button__icon`]]: !isArchiveIcon },
        { [$style[`button__archive`]]: isArchiveIcon },
      ]"
    />
    <template v-if="!isHiddenLabel">
      {{ label }}
    </template>
    <template v-if="hasAlarm">
      <span :class="$style[`button__alarm`]"></span>
    </template>
    <template v-if="isArrowButton">
      <span :class="$style[`button__arrow__wrapper`]" aria-hidden="true">
        <span :class="$style[`button__arrow`]">
          <svgIcon
            name="head-arrow"
            aria-hidden="true"
            :width="7"
            :height="7"
            :class="$style[`button__arrow__head`]"
          />
        </span>
      </span>
    </template>
  </button>
</template>

<script>
export default {
  props: {
    type: {
      type: String,
      required: true,
      default: `button`,
      validator(type) {
        return [`button`, `reset`, `submit`].includes(type);
      },
    },
    context: {
      type: String,
      required: false,
      default: `inline`,
      validator(context) {
        return [`inline`, `block`].includes(context);
      },
    },
    disabled: {
      type: Boolean,
      required: false,
      default: false,
    },
    inactive: {
      type: Boolean,
      required: false,
      default: false,
    },
    appearance: {
      type: String,
      required: true,
      default: `basic`,
      validator(appearance) {
        return [
          `basic`,
          `cancel`,
          `icon`,
          `icon-text`,
          `text-arrow`,
          `circle`,
        ].includes(appearance);
      },
    },
    colorType: {
      type: String,
      required: false,
      default: ``,
      validator(color) {
        return [``, `grey`].includes(color);
      },
    },
    theme: {
      type: [String, null],
      required: false,
      default: null,
      validator(theme) {
        return theme === null || [`bright`, `dark`, `ghost`].includes(theme);
      },
    },
    size: {
      type: String,
      required: true,
      default: `small`,
      validator(size) {
        return [`small`, `medium`, `large`, `x-large`].includes(size);
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
    hideLabel: {
      type: Boolean,
      required: false,
      default: false,
    },
    icon: {
      type: String,
      required: false,
      default: ``,
    },
    pressed: {
      type: Boolean,
      required: false,
      default: false,
    },
    hasAlarm: {
      type: Boolean,
      required: false,
      default: false,
    },
  },
  data() {
    return {
      newAlarmMsg: `새로운 알림 있음`,
    };
  },
  computed: {
    isBlock() {
      return this.context === `block`;
    },
    isDisabled() {
      return this.disabled || this.inactive;
    },
    isIconType() {
      return this.appearance.includes(`icon`) || this.isCircle;
    },
    isHiddenLabel() {
      return this.hideLabel || this.appearance === `icon` || this.isCircle;
    },
    isArchiveIcon() {
      return [`contents-add`, `contents-remove`].includes(this.icon);
    },
    svgSize() {
      return {
        small: 24,
        medium: 32,
      }[this.size];
    },
    isArrowButton() {
      return this.appearance.includes(`arrow`);
    },
    isCircle() {
      return this.appearance.includes(`circle`);
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
    focus() {
      const { button } = this.$refs;
      button && button.focus();
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
