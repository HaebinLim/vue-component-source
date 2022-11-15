<template>
  <div :class="$style[`footer`]">
    <div :class="[`grid`, $style[`footer__wrap`]]">
      <div :class="$style[`footer__content`]">
        <Logo :bg-mode="`dark`" />
        <FooterContent :class="$style[`footer__links`]" />
      </div>
      <FooterUtil :class="$style[`footer__copyright`]" />
    </div>
  </div>
</template>

<script>
import Logo from '@atoms/Logo/index';
import FooterContent from '@molecules/FooterContent/index';
import FooterUtil from '@molecules/FooterUtil/index';

export default {
  components: {
    Logo,
    FooterContent,
    FooterUtil,
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
