<template>
  <link-text
    target="_blank"
    appearance="basic"
    :href="targetUrl"
    :class="[$style[`banner`], { [$style[`banner--full`]]: stretched }]"
  >
    <picture>
      <source media="(min-width: 1300px)" :srcset="pcImgSrc" />
      <img :src="mImgSrc" :alt="altText" :class="[$style[`banner__img`]]" />
    </picture>
  </link-text>
</template>
<script>
import LinkText from '@atoms/Link/Text/index';
export default {
  components: {
    LinkText,
  },
  props: {
    mImgSrc: {
      type: String,
      required: true,
      default: ``,
    },
    pcImgSrc: {
      type: String,
      required: true,
      default: ``,
    },
    targetUrl: {
      type: String,
      required: true,
      default: ``,
    },
    altText: {
      type: String,
      required: true,
      default: ``,
    },
    stretched: {
      type: Boolean,
      required: false,
      default: false,
    },
  },
};
</script>
<style lang="scss" module src="./index.scss"></style>
