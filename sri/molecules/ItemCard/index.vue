<template>
  <div
    :class="[
      $style[`item`],
      { [$style[`item--${appearance}`]]: appearance !== `basic` },
      { [$style[`item--emphasis`]]: isEmphasis },
    ]"
  >
    <template v-if="!isSkeleton">
      <div :class="$style[`item__info-wrap`]">
        <LinkText
          v-if="useOptionalContent.category"
          :label="category"
          :appearance="`basic`"
          :to="categoryLink"
          :class="$style[`item__category`]"
          :aria-label="`${category} 글 모아보기`"
        />
        <link-text
          :appearance="`basic`"
          v-bind="articleLink"
          :class="$style[`item__title__link`]"
        >
          <span :class="$style[`item__title`]">
            {{ title }}
          </span>
          <span v-if="useOptionalContent.desc" :class="$style[`item__desc`]">
            {{ desc }}
          </span>
        </link-text>
        <LinkText
          v-if="useOptionalContent.editorName"
          :label="editorName"
          :appearance="`basic`"
          :to="editorLink"
          :class="$style[`item__editor`]"
          :aria-label="`${editorName}님의 글 모아보기`"
        />
        <div
          v-if="useOptionalContent.replies || useOptionalContent.likes"
          :class="$style[`item__meta`]"
          tabindex="-1"
          @click="handleArticleLink"
        >
          <template v-if="useOptionalContent.replies">
            <span>댓글</span>
            <span>{{ repliesCount }}</span>
          </template>
          <template v-if="useOptionalContent.likes">
            <span>좋아요</span>
            <span>{{ likesCount }}</span>
          </template>
        </div>
      </div>
      <div :class="$style[`item__img-wrap`]">
        <img :src="image" tabindex="-1" alt="" @click="handleArticleLink" />
        <CheckBox
          v-if="useOptionalContent.checked"
          :context="`block`"
          :label="title"
          :show-label="false"
          :name="`checkbox_${articleId}`"
          :value="articleId"
          :checked="checked"
          :class="[$style[`item__util`], $style[`item__util__check`]]"
          @change="handleChange"
        />
        <ButtonBox
          :appearance="`icon`"
          :label="archived ? `담은 콘텐츠에서 제거` : `담은 콘텐츠에 추가`"
          :type="`button`"
          :hide-label="true"
          :size="`small`"
          :icon="archived ? `contents-remove` : `contents-add`"
          :class="[$style[`item__util`], $style[`item__util__archive`]]"
          @click="handleClick"
        />
      </div>
    </template>
    <template v-else>
      <div :class="$style[`item__info-wrap`]">
        <span
          v-if="useOptionalContent.category"
          :class="$style[`item__category`]"
        >
          <span :class="$style[`skeleton__txt`]"></span>
        </span>
        <span :class="$style[`item__title`]">
          <span :class="$style[`skeleton__txt`]"></span>
        </span>
        <span v-if="useOptionalContent.desc" :class="$style[`item__desc`]">
          <span :class="$style[`skeleton__txt`]"></span>
        </span>
        <span
          v-if="useOptionalContent.editorName"
          :class="$style[`item__editor`]"
        >
          <span :class="$style[`skeleton__txt`]"></span>
        </span>
        <div
          v-if="useOptionalContent.replies || useOptionalContent.likes"
          :class="$style[`item__meta`]"
        >
          <template v-if="useOptionalContent.replies">
            <span><span :class="$style[`skeleton__txt`]"></span></span>
            <span><span :class="$style[`skeleton__txt`]"></span></span>
          </template>
          <template v-if="useOptionalContent.likes">
            <span><span :class="$style[`skeleton__txt`]"></span></span>
            <span><span :class="$style[`skeleton__txt`]"></span></span>
          </template>
        </div>
      </div>
      <div :class="$style[`item__img-wrap`]">
        <span :class="$style[`skeleton__img`]"></span>
      </div>
    </template>
  </div>
</template>
<script>
import LinkText from '@atoms/Link/Text/index';
import CheckBox from '@atoms/CheckBox/index';
import ButtonBox from '@atoms/Button/Box/index';
import { toStringMaxedNumber } from '@/plugins/utils.js';
export default {
  components: {
    LinkText,
    CheckBox,
    ButtonBox,
  },
  props: {
    /**
     * 'simple' : 메인 큐레이션 글목록, 연관 글목록 등에서 사용하는 간소화된 형태 / 'bar' : 메인 최근 글목록에서 사용하는 bar 형태
     */
    appearance: {
      type: String,
      required: false,
      default: `basic`,
      validator(type) {
        return [`basic`, `simple`, `bar`].includes(type);
      },
    },
    /**
     * 메인 큐레이션 글목록 / 카테고리 글목록 등의 첫글에서 사용되는 강조 형태
     */
    isEmphasis: {
      type: Boolean,
      required: false,
      default: false,
    },
    /**
     * 글 고유ID (링크에 사용)
     */
    articleId: {
      type: String,
      required: true,
    },
    /**
     * 글 제목
     */
    title: {
      type: [String, null],
      required: false,
      default: null,
    },
    /**
     * 커버 이미지 경로
     */
    image: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * 담기 상태
     */
    archived: {
      type: Boolean,
      required: false,
      default: false,
    },
    /**
     * 카테고리 명 (미지정시 비노출/링크에 사용)
     */
    category: {
      type: [String, null],
      required: false,
      default: null,
    },
    /**
     * 카테고리 명이 미지정인 경우 라우터 경로등에 사용할 카테고리명 (링크에 사용)
     */
    listCategory: {
      type: [String, null],
      required: false,
      default: null,
    },
    /**
     * 글 본문 (미지정시 비노출)
     */
    desc: {
      type: [String, null],
      required: false,
      default: null,
    },
    /**
     * 댓글 갯수 (미지정시 비노출)
     */
    replies: {
      type: [Number, null],
      required: false,
      default: null,
    },
    /**
     * 좋아요 갯수 (미지정시 비노출)
     */
    likes: {
      type: [Number, null],
      required: false,
      default: null,
    },
    /**
     * 작성자명 (미지정시 비노출)
     */
    editorName: {
      type: [String, null],
      required: false,
      default: null,
    },
    /**
     * 작성자 고유ID (링크에 사용)
     */
    editorId: {
      type: [String, null],
      required: false,
      default: null,
    },
    /**
     * 일괄선택등을 위한 체크박스 체크 여부 (미지정시 비노출)
     */
    checked: {
      type: [Boolean, null],
      required: false,
      default: null,
    },
    /**
     * 본문 링크시 새창에서 열기
     */
    isNewWindow: {
      type: Boolean,
      required: false,
      default: false,
    },
    /**
     * SkeletonUI 로 노출 여부
     */
    isSkeleton: {
      type: Boolean,
      required: false,
      default: false,
    },
    /**
     * Optional 항목들의 노출 여부 정의
     */
    useOptionalContent: {
      type: Object,
      required: true,
      default: () => {
        return {
          category: true,
          desc: true,
          editorName: true,
          checked: true,
          replies: true,
          likes: true,
        };
      },
    },
  },
  computed: {
    likesCount() {
      return this.useOptionalContent.likes
        ? toStringMaxedNumber(this.likes, 9999)
        : ``;
    },
    repliesCount() {
      return this.useOptionalContent.replies
        ? toStringMaxedNumber(this.replies, 9999)
        : ``;
    },
    categoryLink() {
      return !this.category ? `/${this.listCategory}` : `/${this.category}`;
    },
    articleUrl() {
      return !this.category
        ? `/${this.listCategory}/${this.articleId}`
        : `/${this.category}/${this.articleId}`;
    },
    articleLink() {
      return this.isNewWindow
        ? {
            href: this.articleUrl,
            target: '_blank',
          }
        : {
            to: this.articleUrl,
          };
    },
    editorLink() {
      return `/editor/${this.editorId}`;
    },
  },
  methods: {
    handleArticleLink(e) {
      return this.isNewWindow
        ? window.open(this.articleUrl)
        : this.$router.push(this.articleUrl);
    },
    handleChange(p) {
      this.$emit('change-selected', p);
    },
    handleClick() {
      this.$emit('change-archive', {
        value: this.articleId,
        action: this.archived ? `remove` : `add`,
      });
    },
  },
};
</script>
<style lang="scss" module src="./index.scss"></style>
