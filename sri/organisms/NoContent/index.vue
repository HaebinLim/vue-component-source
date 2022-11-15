<template>
  <div
    :class="[
      [`grid`],
      $style[`no-content`],
      { [$style[`no-content--tag`]]: tagData.length },
    ]"
  >
    <Heading
      :rank="1"
      :text-content="pageTitle"
      :class="$style[`no-content__title`]"
    />
    <p v-if="pageDesc" :class="$style[`no-content__desc`]">{{ pageDesc }}</p>
    <BoxButton
      v-if="isError || btnText"
      :appearance="`text-arrow`"
      :type="`button`"
      :size="`small`"
      :label="isError ? `뒤로 가기` : btnText"
      :theme="`ghost`"
      :class="$style[`no-content__link`]"
      @click="handleLinkClick"
    />
    <div v-if="tagData.length > 0" :class="$style[`no-content__tags`]">
      <PostHashtags
        :heading-rank="2"
        :heading-text="`추천해시태그`"
        :hashtags="tagData"
      />
    </div>
  </div>
</template>

<script>
import Heading from '@atoms/Heading/index';
import BoxButton from '@atoms/Button/Box/index';
import PostHashtags from '@organisms/PostHashtags';
export default {
  components: {
    Heading,
    BoxButton,
    PostHashtags,
  },
  props: {
    /**
     * 에러 결과 페이지인지 체크 (링크 텍스트 및 링크 동작에 영향미침)
     */
    isError: {
      type: Boolean,
      required: false,
      default: false,
    },
    /**
     * 제목 텍스트
     */
    pageTitle: {
      type: String,
      required: true,
      default: ``,
    },
    /**
     * 내용 텍스트
     */
    pageDesc: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * 버튼 링크 이동시 router-link
     */
    btnLink: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * 버튼 텍스트
     */
    btnText: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * API에서 받아온 추천 태그 목록
     */
    tagData: {
      type: Array,
      required: false,
      default: () => [],
    },
  },
  methods: {
    handleLinkClick() {
      const prev = this.$nuxt.context.from?.path;
      this.isError
        ? prev
          ? this.$router.go(-1)
          : this.$router.replace('/')
        : this.$router.push(this.btnLink);
    },
  },
};
</script>
<style lang="scss" module src="./index.scss"></style>
