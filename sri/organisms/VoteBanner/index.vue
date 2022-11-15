<template>
  <div :class="[`grid`, $style[`vote`]]">
    <div :class="$style[`vote__content`]">
      <Heading
        :rank="headingRank"
        :class="$style[`vote__title`]"
        :text-content="`Vote`"
      />
      <div :class="$style[`vote__topic`]">{{ title }}</div>
      <BoxButton
        :type="`button`"
        :appearance="`text-arrow`"
        :size="`large`"
        :label="`투표하러 가기`"
        :theme="`ghost`"
        :class="$style[`vote__link`]"
        @click="handleVoteLink"
      />
    </div>
    <a :href="targetUrl" :target="`_blank`" :class="$style[`vote__banner`]">
      <picture>
        <source
          media="(min-width: 768px)"
          :srcset="$resolveImageUrl(pcImgSrc)"
        />
        <img
          :src="$resolveImageUrl(mImgSrc)"
          :alt="altText"
          :class="$style[`vote__img`]"
        />
      </picture>
    </a>
  </div>
</template>

<script>
import Heading from '@atoms/Heading/index';
import BoxButton from '@atoms/Button/Box/index';

export default {
  components: {
    Heading,
    BoxButton,
  },
  props: {
    headingRank: {
      type: Number,
      required: false,
      default: 1,
    },
    title: {
      type: String,
      required: true,
      default: ``,
    },
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
  },
  methods: {
    handleVoteLink() {
      return window.open(this.targetUrl);
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
