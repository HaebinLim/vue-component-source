<template>
  <div :class="[`grid`]">
    <section
      aria-roledescription="carousel"
      :aria-labelledby="`best-heading-${id}`"
      :class="[$style[`best`]]"
    >
      <div :class="[$style[`best__header`]]">
        <Heading
          :id="`best-heading-${id}`"
          :rank="headingRank"
          :text-content="`Best Top 5`"
          :class="$style[`best__heading`]"
        />
        <div :class="$style[`best__controls`]">
          <BoxButton
            :type="`button`"
            :appearance="`icon`"
            :size="`small`"
            :label="autoplaying ? `자동 넘김 일시 정지` : `자동 넘김 시작`"
            :icon="autoplaying ? `best-pause` : `best-play`"
            :class="$style[`best__controls__autoplay`]"
            @click="toggleAutoPlaying"
          />
          <BoxButton
            :type="`button`"
            :appearance="`arrow-left`"
            :size="`large`"
            :label="`이전 슬라이드`"
            :class="[`flicking-arrow-prev`]"
            @click="prevSlide"
          />
          <BoxButton
            :type="`button`"
            :appearance="`arrow-right`"
            :size="`large`"
            :label="`다음 슬라이드`"
            :class="[`flicking-arrow-next`]"
            @click="nextSlide"
          />
        </div>
      </div>
      <div
        :class="[{ 'grid--full': !hasPcResolution }, $style[`best__carousel`]]"
      >
        <component
          :is="tmpl"
          ref="carousel"
          :list="list"
          :update-archive="updateArchive"
          :breakpoint="hasPcResolution ? null : breakpoint"
          :autoplaying="autoplaying"
          :dealay="3000"
          :duration="500"
        />
      </div>
    </section>
  </div>
</template>

<script>
import Heading from '@atoms/Heading';
import BoxButton from '@atoms/Button/Box/index';

export default {
  components: {
    Heading,
    BoxButton,
    mobile: () => import(`./mobile`),
    pc: () => import(`./pc`),
  },
  props: {
    /**
     * breakpoint 기준 현재 해상도 정보
     */
    breakpoint: {
      type: String,
      required: true,
      default: `mobile`,
      validator(bp) {
        return [`mobile`, `tablet`, `pc`].includes(bp);
      },
    },
    /**
     * 섹션 헤딩 랭크
     */
    headingRank: {
      type: Number,
      required: true,
      default: 2,
    },
    /**
     * best top 5 컨텐츠 목록
     */
    list: {
      type: Array,
      required: true,
      default: () => [],
    },
    /**
     * 담기 설정/해제 함수
     */
    updateArchive: {
      type: Function,
      required: true,
    },
  },
  data() {
    return {
      autoplaying: true,
    };
  },
  computed: {
    id() {
      return this.$_uid;
    },
    hasPcResolution() {
      return this.breakpoint === `pc`;
    },
    tmpl() {
      const template = this.hasPcResolution ? `pc` : `mobile`;
      return template;
    },
  },
  methods: {
    toggleAutoPlaying() {
      this.autoplaying = !this.autoplaying;
    },
    prevSlide() {
      this.$refs.carousel.prevSlide();
    },
    nextSlide() {
      this.$refs.carousel.nextSlide();
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
