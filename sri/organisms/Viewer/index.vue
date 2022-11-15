<template>
  <div :class="[$style[`viewer`], $style[`viewer--${articleType}`]]">
    <!-- @slot 컨텐츠 슬롯 -->
    <slot></slot>
  </div>
</template>
<script>
export default {
  props: {
    /**
     * 아티클 타입
     */
    articleType: {
      type: String,
      required: false,
      default: `card`,
      validator(type) {
        return [`card`, `talk`].includes(type);
      },
    },
  },
};
</script>
<style lang="scss" module src="./index.scss"></style>
