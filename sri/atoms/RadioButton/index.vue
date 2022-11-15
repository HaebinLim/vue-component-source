<template>
  <span :class="[$style['rdo'], { [$style[`rdo--inline`]]: !isBlock }]">
    <input
      :id="id"
      v-model="picked"
      type="radio"
      :name="name"
      :disabled="disabled"
      :value="value"
      :class="[$style['rdo__input']]"
      @change="handleChange"
    />
    <label
      :for="id"
      :class="[
        $style['rdo__label'],
        { [$style['rdo__label--hide']]: !showLabel },
      ]"
    >
      {{ label }}
    </label>
  </span>
</template>
<script>
export default {
  props: {
    checked: {
      type: Boolean,
      required: false,
      default: false,
    },
    context: {
      type: String,
      required: false,
      default: `inline`,
      validator(ctx) {
        return [`inline`, `block`].includes(ctx);
      },
    },
    disabled: {
      type: Boolean,
      required: false,
      default: false,
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
      required: true,
      default: ``,
    },
    value: {
      type: String,
      required: true,
      default: ``,
    },
  },
  data() {
    return {
      id: `radio-${this.$_uid}`,
      picked: this.checked ? this.value : '',
    };
  },
  computed: {
    isBlock() {
      return this.context === `block`;
    },
  },
  watch: {
    checked(val) {
      this.picked = val ? this.value : '';
    },
  },
  methods: {
    handleChange() {
      /**
       * 선택 상태 변경 시 발생 <br>
       * `Boolean`
       *
       * @event change
       */
      this.$emit('change', this.picked);
    },
  },
};
</script>
<style lang="scss" module src="./index.scss"></style>
