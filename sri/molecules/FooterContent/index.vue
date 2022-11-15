<template>
  <div :class="$style[`footer`]">
    <p :class="$style[`footer__tagline`]">
      지식과 정보가 <br />
      즐거움이 되는, <br />
      더플랩 인사이트
    </p>
    <div :class="$style[`footer__links`]">
      <LinkText
        :to="`/news-letter`"
        :label="`뉴스레터 모아보기`"
        :appearance="`basic`"
        :theme="`dark`"
        :use-under-line="true"
        :has-under-line="true"
        :class="$style[`footer__link--newsletter`]"
      />
      <div :class="$style[`footer__sns`]">
        <link-text
          v-for="sns in snsLinks"
          :key="sns.name"
          :href="`${sns.href}`"
          :target="`_blank`"
          :class="$style[`footer__link--sns`]"
          :appearance="`basic`"
          :aria-label="sns.name"
        >
          <svgIcon
            :name="`${sns.icon}`"
            :width="18"
            :height="18"
            :aria-hidden="true"
            :class="$style[`icon__sns`]"
          />
        </link-text>
      </div>
    </div>
  </div>
</template>

<script>
import LinkText from '@atoms/Link/Text/index.vue';
export default {
  components: {
    LinkText,
  },
  data() {
    return {
      snsLinks: [
        {
          icon: 'sns-linkedin',
          name: 'linkedin',
          href: '#',
        },
        {
          icon: 'sns-facebook-footer',
          name: 'facebook',
          href: '#',
        },
        {
          icon: 'sns-youtube',
          name: 'youbute',
          href: '#',
        },
        {
          icon: 'sns-instagram',
          name: 'instagram',
          href: '#',
        },
      ],
    };
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
