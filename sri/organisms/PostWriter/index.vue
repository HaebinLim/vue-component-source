<template>
  <div :class="[$style[`writer`], type !== `` && $style[`writer--${type}`]]">
    <div :class="$style[`writer__info`]">
      <Heading
        :rank="headingRank"
        :text-content="`Writer`"
        :class="$style[`writer__title`]"
      />
      <div :class="$style[`writer__name`]">{{ editor.name }}</div>
      <div v-if="isNotEmpty" :class="$style[`writer__intro`]">
        {{ editor.intro }}
      </div>
      <link-text
        :to="`/editor/${editor.id}`"
        :appearance="`basic`"
        :drop-user-line-space="true"
        :class="$style[`writer__btn`]"
      >
        작성글 보기
        <span :class="$style[`writer__arrow`]" aria-hidden="true">
          <svgIcon name="head-arrow" :width="7" :height="7" />
        </span>
      </link-text>
    </div>
    <ProfileImage
      :type="`writer`"
      :image-url="editor.image || ``"
      :class="$style[`writer__image`]"
    />
  </div>
</template>

<script>
import LinkText from '@atoms/Link/Text/index';
import ProfileImage from '@atoms/ProfileImage/index';
import Heading from '@atoms/Heading/index';

export default {
  components: {
    LinkText,
    ProfileImage,
    Heading,
  },
  props: {
    type: {
      type: String,
      required: false,
      default: ``,
      validator(type) {
        return [``, `card`].includes(type);
      },
    },
    headingRank: {
      type: Number,
      required: false,
      default: 1,
    },
    editor: {
      type: Object,
      required: true,
      default: () => {
        return {};
      },
    },
  },
  computed: {
    isNotEmpty() {
      return !!this.editor.intro;
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
