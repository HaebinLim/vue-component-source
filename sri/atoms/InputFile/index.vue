<template>
  <span :class="$style[`file-selector`]">
    <BoxButton
      :type="`button`"
      :context="`block`"
      :disabled="disabled"
      :appearance="`basic`"
      :size="`large`"
      :label="`첨부파일 추가`"
      :class="$style[`file-selector__button`]"
      @click="handleClick"
    />
    <input
      ref="input"
      type="file"
      :disabled="disabled"
      :class="$style[`file-selector__input`]"
      @change="handleChange"
    />
  </span>
</template>

<script>
import BoxButton from '@atoms/Button/Box';

export default {
  components: {
    BoxButton,
  },
  props: {
    /**
     * 비활성 여부
     */
    disabled: {
      type: Boolean,
      required: false,
      default: false,
    },
  },
  methods: {
    handleClick() {
      const { input } = this.$refs;
      input.click();
    },
    handleChange(event) {
      const [file] = event.target.files;
      /**
       * 첨부 파일 선택 혹은 변경 시 발생 <br>
       * `File`
       *
       * @event change
       */
      this.$emit(`change`, file);
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
