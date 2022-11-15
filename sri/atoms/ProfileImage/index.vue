<template>
  <div
    :class="[
      $style[`profile__image`],
      type && $style[`profile__image--${type}`],
      useBorder && $style[`profile__image--border`],
    ]"
    :style="style"
  ></div>
</template>

<script>
export default {
  props: {
    type: {
      type: String,
      required: true,
      default: `writer`,
      validator(type) {
        return [`writer`, `editor`, `talker`].includes(type);
      },
    },
    imageUrl: {
      type: String,
      required: true,
      default: ``,
    },
    useBorder: {
      type: Boolean,
      required: false,
      default: false,
    },
  },
  computed: {
    style() {
      return {
        backgroundImage: `url(${this.imageUrl})`,
      };
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
