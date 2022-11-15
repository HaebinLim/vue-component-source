<template>
  <div :class="$style[`banner`]">
    <div
      :class="[`grid`, $style[`banner__wrap`]]"
      @mouseenter="handleTransitionStop"
      @mouseleave="handleTransitionResume"
    >
      <heading :rank="2" :class="$style[`banner__title`]">
        {{ bannerTitle }}
      </heading>

      <transition-group
        :tag="`div`"
        name="recruit__banner"
        :class="$style[`banner__wrap_items`]"
      >
        <link-text
          v-for="({ title, url }, index) in bannerData"
          v-show="index === currentIdx"
          :key="title"
          :class="$style[`banner__item`]"
          :href="url"
          :appearance="`basic`"
          :drop-user-line-space="true"
          :target="`_blank`"
          @focus.native="handleTransitionStop"
          @blur.native="handleTransitionResume"
        >
          {{ title }}
        </link-text>
      </transition-group>
      <BoxButton
        :appearance="`text-arrow`"
        :type="`button`"
        :size="`small`"
        :label="bannerLinkText"
        :theme="`ghost`"
        :class="$style[`banner__link`]"
        @click="handleBannerLink"
        @focus.native="handleTransitionStop"
        @blur.native="handleTransitionResume"
      />
    </div>
  </div>
</template>
<script>
import Heading from '@atoms/Heading/index';
import LinkText from '@atoms/Link/Text/index';
import BoxButton from '@atoms/Button/Box/index';
export default {
  components: {
    Heading,
    LinkText,
    BoxButton,
  },
  props: {
    /**
     * 배너 영역 제목
     */
    bannerTitle: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * 배너 링크 텍스트
     */
    bannerLinkText: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * 배너당 롤링 타이머(ms)
     */
    duration: {
      type: Number,
      required: false,
      default: 0,
    },
    /**
     * API에서 받아온 배너 데이터
     */
    bannerData: {
      type: Array,
      required: false,
      default: () => {
        return [];
      },
    },
  },
  data() {
    return {
      transitionTimer: null,
      currentIdx: 0,
    };
  },
  computed: {
    max() {
      return this.bannerData.length;
    },
  },
  mounted() {
    this.handleTransitionResume();
  },
  beforeDestroy() {
    this.handleTransitionStop();
  },
  methods: {
    handleTransitionStop(e) {
      clearTimeout(this.transitionTimer);
    },
    handleTransitionResume(e) {
      clearTimeout(this.transitionTimer);
      this.transitionTimer = setTimeout(() => {
        this.currentIdx = (this.currentIdx + 1) % this.max;
        this.handleTransitionResume();
      }, this.duration);
    },
    handleBannerLink(e) {
      return window.open(this.bannerData[this.currentIdx].url);
    },
  },
};
</script>
<style lang="scss" module src="./index.scss"></style>
<style lang="scss" src="./transition.scss"></style>
