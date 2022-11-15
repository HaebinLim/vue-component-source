<template>
  <div :class="$style[`webina`]">
    <div :class="$style[`webina__wrap`]">
      <div :class="$style[`webina__content`]">
        <Heading
          :rank="headingRank"
          :class="$style[`webina__title`]"
          :text-content="subTitle"
        />
        <div :class="$style[`webina__topic`]">{{ title }}</div>
        <dl :class="$style[`webina__info`]">
          <dt :class="$style[`webina__info__tit`]">일시</dt>
          <dd :class="$style[`webina__info__data`]">{{ dateTimeText }}</dd>
          <dt :class="$style[`webina__info__tit`]">장소</dt>
          <dd :class="$style[`webina__info__data`]">{{ place }}</dd>
        </dl>
        <BoxButton
          :type="`button`"
          :appearance="`text-arrow`"
          :size="`large`"
          :label="`더 알아보기`"
          :class="$style[`webina__link`]"
          @click="handleWebinaLink"
        />
      </div>
      <div :class="$style[`webina__banner`]" aria-hidden="true">
        <picture>
          <source
            media="(min-width: 1300px)"
            :srcset="$resolveImageUrl(pcImgSrc)"
          />
          <img
            :src="$resolveImageUrl(mImgSrc)"
            alt=""
            :class="[$style[`webina__img`]]"
          />
        </picture>
      </div>
    </div>
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
    /**
     * 타이틀 헤딩 레벨
     */
    headingRank: {
      type: Number,
      required: false,
      default: 1,
    },
    /**
     * 행사 배너 제목
     */
    subTitle: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * 행사 배너 내용
     */
    title: {
      type: String,
      required: true,
      default: ``,
    },
    /**
     * 행사 일시
     */
    dateTimeText: {
      type: String,
      required: true,
      default: ``,
    },
    /**
     * 행사 장소
     */
    place: {
      type: String,
      required: true,
      default: ``,
    },
    /**
     * 모바일 이미지 주소
     */
    mImgSrc: {
      type: String,
      required: true,
      default: ``,
    },
    /**
     * PC 이미지 주소
     */
    pcImgSrc: {
      type: String,
      required: true,
      default: ``,
    },
    /**
     * 클릭 시 이동할 링크 URL
     */
    targetUrl: {
      type: String,
      required: true,
      default: ``,
    },
  },
  methods: {
    handleWebinaLink() {
      return window.open(this.targetUrl);
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
