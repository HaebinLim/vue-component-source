<template>
  <span
    :class="[
      $style[`inputbox`],
      status && $style[`inputbox--${status}`],
      { [$style[`inputbox--inline`]]: !isBlock },
    ]"
  >
    <label
      :for="id"
      :class="[
        $style[`inputbox__label`],
        { [$style[`inputbox__label--hide`]]: !showLabel },
      ]"
    >
      {{ label }}
    </label>
    <span :class="[$style[`inputbox__wrap`]]">
      <input
        :id="id"
        :type="type"
        :name="name"
        :readonly="readonly"
        :disabled="disabled"
        :placeholder="placeholder"
        :spellcheck="spellcheck"
        :autocomplete="autocomplete"
        :value.prop="value"
        :maxlength="maxlength"
        :class="[$style[`inputbox__input`]]"
        @input="handleInput"
        v-on="!!handleClick ? { click: handleClick } : {}"
      />
      <svg-icon
        v-if="icon"
        :name="icon"
        :width="24"
        :height="24"
        :class="[$style[`inputbox__icon`]]"
      />
    </span>
    <span v-if="feedbackMsg" :class="$style[`inputbox__feedback`]">
      {{ feedbackMsg }}
    </span>
  </span>
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
    type: {
      type: String,
      required: true,
      default: `text`,
      validator(type) {
        return [`text`, `password`, `search`].includes(type);
      },
    },
    name: {
      type: [String, null],
      required: false,
      default: ``,
    },
    readonly: {
      type: Boolean,
      required: false,
      default: false,
    },
    placeholder: {
      type: [String, null],
      required: false,
      default: ``,
    },
    spellcheck: {
      type: Boolean,
      required: false,
      default: false,
    },
    autocomplete: {
      type: Boolean,
      required: false,
      default: false,
    },
    value: {
      type: String,
      required: true,
      default: ``,
    },
    maxlength: {
      type: [Number, null],
      required: false,
      default: null,
    },
    feedbackMsg: {
      type: String,
      required: false,
      default: ``,
    },
    status: {
      type: String,
      required: false,
      default: ``,
      validator(status) {
        return [``, `error`, `notify`, `verified`, `disabled`].includes(status);
      },
    },
    handleClick: {
      type: Function,
      required: false,
      default: null,
    },
  },
  data() {
    return {
      id: `inputbox-${this.$_uid}`,
    };
  },
  computed: {
    disabled() {
      return this.status === `disabled`;
    },
    isBlock() {
      return this.context === `block`;
    },
    useClick() {
      return !!this.handleClick;
    },
    icon() {
      return { error: `error`, verified: `check` }[this.status] || null;
    },
  },
  methods: {
    handleInput(event) {
      /**
       * 입력 시 발생 이벤트, payload: 입력 된 값
       *
       * @event update:value
       */
      this.$emit(`update:value`, event.target.value);
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
