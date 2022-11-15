<template>
  <span
    :class="[
      $style['selectbox'],
      { [$style[`selectbox--large`]]: !isBasic },
      { [$style[`selectbox--inline`]]: !isBlock },
      { [$style[`--selected`]]: !isSelected },
      { [$style['--disabled']]: disabled },
    ]"
  >
    <label
      :for="id"
      :class="[
        $style['selectbox__label'],
        { [$style['selectbox__label--hide']]: !showLabel },
      ]"
    >
      {{ label }}
    </label>
    <span :class="[$style[`selectbox__wrap`]]">
      <select
        :id="id"
        v-model="selected"
        :name="name"
        :disabled="disabled"
        :class="[$style['selectbox__select']]"
        @change="handleChange"
      >
        <template
          v-for="{
            text: optionText,
            disabled: isDisabled,
            value: optionValue,
          } in mergedOptions"
        >
          <option
            :key="optionText"
            :disabled="!!isDisabled"
            :value="optionValue"
          >
            {{ optionText }}
          </option>
        </template>
      </select>
      <svg-icon
        name="dropdown"
        :width="24"
        :height="24"
        :class="[$style[`selectbox__icon`]]"
      />
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
    type: {
      type: String,
      required: false,
      default: `basic`,
      validator(tp) {
        return [`basic`, `large`].includes(tp);
      },
    },
    disabled: {
      type: Boolean,
      required: false,
      default: false,
    },
    defaultOption: {
      type: String,
      required: false,
      default: ``,
    },
    label: {
      type: String,
      required: true,
      default: ``,
    },
    showLabel: {
      type: Boolean,
      required: false,
      default: false,
    },
    name: {
      type: String,
      required: false,
      default: ``,
    },
    value: {
      type: String,
      required: false,
      default: ``,
    },
    options: {
      type: Array,
      required: true,
      default: () => [],
    },
  },
  data() {
    return {
      id: `select-${this.$_uid}`,
      selected: this.value,
    };
  },
  computed: {
    mergedOptions() {
      return this.defaultOption
        ? [
            { disabled: true, text: this.defaultOption, value: '' },
            ...this.options,
          ]
        : this.options;
    },
    isSelected() {
      return this.selected === ``;
    },
    isBasic() {
      return this.type === `basic`;
    },
    isBlock() {
      return this.context === `block`;
    },
  },
  watch: {
    value(val) {
      this.selected = val;
    },
  },
  methods: {
    handleChange() {
      /**
       * 선택 값 변경 시 발생 <br>
       * `any` (selected value)
       *
       * @event change
       */
      this.$emit('change', this.selected);
    },
  },
};
</script>
<style lang="scss" module src="./index.scss"></style>
