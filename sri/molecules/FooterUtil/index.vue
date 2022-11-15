<template>
  <div :class="$style[`footer`]">
    <div :class="$style[`footer__policy`]">
      <LinkText
        :href="``"
        :label="`이용약관`"
        :appearance="`grey`"
        :theme="`dark`"
        :target="`_blank`"
        :class="$style[`footer__link`]"
      />
      <LinkText
        :href="``"
        :label="`개인정보처리방침`"
        :appearance="`grey`"
        :theme="`dark`"
        :target="`_blank`"
        :class="$style[`footer__link`]"
      />
      <LinkText
        :to="`/cs/faq`"
        :label="`고객센터`"
        :appearance="`grey`"
        :theme="`dark`"
        :class="$style[`footer__link`]"
      />
    </div>
    <div :class="$style[`footer__copy`]">
      <span :class="$style[`footer__txt`]">(주)사람인HR</span>
      <span :class="$style[`footer__txt`]">대표 : 김용환</span>
      <span :class="$style[`footer__txt`]">
        사업자 등록번호 : 113-86-00917
      </span>
      <br />
      <span :class="$style[`footer__txt`]">
        08378, 서울특별시 구로구 디지털로34길 43, 201호
      </span>
      <span :class="[$style[`footer__txt`], $style[`num`]]">
        02 - 2025 - 7777
      </span>
      <br />
      <span :class="$style[`footer__txt`]"> INSIGHT@SARAMIN.CO.KR </span>
      <span :class="$style[`footer__txt`]">
        COPYRIGHT(C) (주)사람인HR. ALL RIGHTS RESERVED.
      </span>
    </div>
  </div>
</template>

<script>
import LinkText from '@atoms/Link/Text/index';
export default {
  components: {
    LinkText,
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
