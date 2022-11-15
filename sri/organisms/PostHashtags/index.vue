<template>
  <div v-if="hashtags.length" :class="$style[`hashtag`]">
    <Heading
      :rank="headingRank"
      :text-content="headingText"
      :class="$style[`hashtag__title`]"
    />
    <div :class="$style[`hashtag__list`]">
      <LinkText
        v-for="tag in hashtags"
        :key="tag"
        :to="`/tag/${tag}`"
        :appearance="`basic`"
        :label="tag"
        :use-under-line="true"
        :has-under-line="true"
        :class="$style[`hashtag__link`]"
      />
    </div>
  </div>
</template>

<script>
import LinkText from '@atoms/Link/Text';
import Heading from '@atoms/Heading/index';

export default {
  components: {
    LinkText,
    Heading,
  },
  props: {
    headingRank: {
      type: Number,
      required: false,
      default: 1,
    },
    headingText: {
      type: String,
      required: false,
      default: `Hashtag`,
    },
    hashtags: {
      type: Array,
      required: false,
      default: () => [],
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
