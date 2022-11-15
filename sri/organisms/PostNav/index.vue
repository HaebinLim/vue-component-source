<template>
  <div :class="$style[`nav`]">
    <PostNavItem
      :nav-type="`prev`"
      :article="prev"
      :class="$style[`nav__item`]"
    />
    <PostNavItem
      :nav-type="`next`"
      :article="next"
      :class="$style[`nav__item`]"
    />
  </div>
</template>

<script>
import PostNavItem from '@molecules/PostNavItem/index';

export default {
  components: {
    PostNavItem,
  },
  props: {
    prev: {
      type: Object,
      required: false,
      default: null,
    },
    next: {
      type: Object,
      required: false,
      default: null,
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
