<template>
  <div :class="$style[`newsletter-view`]">
    <Heading
      :rank="1"
      :text-content="title"
      :class="$style[`newsletter-view__title`]"
    />
    <div :class="$style[`newsletter-view__content`]">
      <link-text
        v-for="({ id, title: cTitle, category, image }, index) in content"
        :key="index"
        :appearance="`basic`"
        :class="$style[`newsletter-view__detail`]"
        :to="`/post/${id}`"
      >
        <img :src="`${image}`" alt="" />
        <span :class="$style[`newsletter-view__detail__cont`]">
          {{ category }}
          <span :class="$style[`newsletter-view__detail__tit`]">
            {{ cTitle }}
          </span>
        </span>
      </link-text>
    </div>
    <div :class="$style[`newsletter-view__nav`]">
      <LinkText
        v-if="!!prev"
        :to="newsletterUrl(`${prev.id}`)"
        :appearance="`basic`"
        :label="`${prev.title}`"
        :aria-label="`이전 글 ${prev.title}`"
        :class="[
          $style[`newsletter-view__link`],
          $style[`newsletter-view__link--prev`],
        ]"
      />
      <LinkText
        v-if="!!next"
        :to="newsletterUrl(`${next.id}`)"
        :appearance="`basic`"
        :label="`${next.title}`"
        :aria-label="`다음 글 ${next.title}`"
        :class="[
          $style[`newsletter-view__link`],
          $style[`newsletter-view__link--next`],
        ]"
      />
    </div>
  </div>
</template>
<script>
import Heading from '@atoms/Heading/index';
import LinkText from '@atoms/Link/Text';

export default {
  components: {
    Heading,
    LinkText,
  },
  props: {
    /**
     * 뉴스레터 타이틀
     */
    title: {
      type: String,
      required: true,
      default: ``,
    },
    /**
     * 뉴스레터 컨텐츠
     */
    content: {
      type: Array,
      required: false,
      default: () => [],
    },
    /**
     * 이전 뉴스레터
     */
    prev: {
      type: Object,
      required: false,
      default: null,
    },
    /**
     * 다음 뉴스레터
     */
    next: {
      type: Object,
      required: false,
      default: null,
    },
  },
  methods: {
    newsletterUrl(date) {
      const [year, month, day] = date.split('-');
      return `/newsletter/${year}/${month + day}`;
    },
  },
};
</script>
<style lang="scss" module src="./index.scss"></style>
