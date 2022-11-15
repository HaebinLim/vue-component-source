<template>
  <div :class="[`grid`, $style[`subscribe__wrapper`]]">
    <section :class="[$style[`subscribe`]]">
      <Heading
        :rank="1"
        :text-content="info.title"
        :class="$style[`subscribe__heading`]"
      />
      <p :class="$style[`subscribe__desc`]" v-html="info.description"></p>
      <BoxLink
        :to="`/`"
        :context="`block`"
        :appearance="`basic`"
        :size="`x-large`"
        :label="info.linkLabel"
        :class="$style[`subscribe__link`]"
      />
    </section>
  </div>
</template>

<script>
import Heading from '@atoms/Heading';
import BoxLink from '@atoms/Link/Box';

export default {
  components: {
    Heading,
    BoxLink,
  },
  props: {
    /**
     * 로그인 여부
     */
    isLoggedIn: {
      type: Boolean,
      required: true,
      default: false,
    },
    /**
     * 구독 상태
     */
    subscribed: {
      type: Boolean,
      requried: true,
      default: false,
    },
  },
  computed: {
    info() {
      const dic = {
        '00': {
          title: `직장인의 한 주 활력, 더플랩 뉴스레터를 무료로 즐기세요!`,
          description: `더플랩 통합 회원으로 가입하시면 다음 회차 뉴스레터부터 자동으로 배달됩니다. <br>마케팅 수신 동의에서 이메일 수신 동의를 체크해 주세요!`,
          linkLabel: `회원가입`,
          linkURL: process.env.JOIN_URL,
          isExternal: true,
        },
        10: {
          title: `이메일 수신 동의를 아직 하지 않으셨군요`,
          description: `하단의 이메일 수신 동의를 누르시면 다음 회차 뉴스레터부터 자동으로 배달됩니다.`,
          linkLabel: `이메일 수신 동의`,
          linkURL: `/member/profile`,
        },
        11: {
          title: `앗! 이미 뉴스레터를 구독하고 계세요.`,
          description: `더플랩 통합 회원으로 가입하시면 다음 회차 뉴스레터부터 자동으로 배달됩니다. <br>마케팅 수신 동의에서 이메일 수신 동의를 체크해 주세요!`,
          linkLabel: `확인`,
          linkURL: `/`,
        },
      };

      return dic[`${+this.isLoggedIn}${+this.subscribed}`];
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
