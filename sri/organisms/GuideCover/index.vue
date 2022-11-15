<template>
  <div :class="$style[`guide-cover`]">
    <div :class="$style[`guide-cover__wrap`]">
      <span
        :class="[
          $style[`guide-cover__arrow`],
          $style[`guide-cover__arrow--prev`],
        ]"
        aria-hidden="true"
      >
        <svgIcon
          name="head-arrow"
          :width="7"
          :height="7"
          :class="$style[`icon__arrow__head`]"
        />
      </span>
      <div>
        <p :class="$style[`guide-cover__title`]">{{ coverTitle }}</p>
        <p :class="$style[`guide-cover__desc`]">{{ coverDesc }}</p>
      </div>
      <span
        :class="[
          $style[`guide-cover__arrow`],
          $style[`guide-cover__arrow--next`],
        ]"
        aria-hidden="true"
      >
        <svgIcon
          name="head-arrow"
          :width="7"
          :height="7"
          :class="$style[`icon__arrow__head`]"
        />
      </span>
    </div>
    <CheckBox
      :label="`힌트 그만보기`"
      :show-label="true"
      :name="`cover_check`"
      :theme="`dark`"
      :value="cookieName"
      :checked="isCookieChecked"
      :class="$style[`guide-cover__checkbox`]"
      @change="handleCheckBoxChange"
    />
    <BoxButton
      :appearance="`icon`"
      :type="`button`"
      :size="`medium`"
      :theme="`dark`"
      :label="`닫기`"
      :hide-label="true"
      :icon="`close`"
      :class="$style[`guide-cover__close`]"
      @click="handleCloseClick"
    />
  </div>
</template>
<script>
import CheckBox from '@atoms/CheckBox/index';
import BoxButton from '@atoms/Button/Box/index';
export default {
  components: {
    CheckBox,
    BoxButton,
  },
  props: {
    /**
     * 커버 텍스트 제목
     */
    coverTitle: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * 커버 텍스트 내용
     */
    coverDesc: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * 쿠키 이름에 사용할 식별자
     */
    cookieId: {
      type: String,
      required: false,
      default: ``,
    },
  },
  data() {
    return {
      isCookieChecked: false,
    };
  },
  computed: {
    cookieName() {
      return `hide_cover_${this.cookieId}`;
    },
  },
  methods: {
    handleCheckBoxChange({ checked }) {
      this.isCookieChecked = checked;
    },
    handleCloseClick() {
      if (this.isCookieChecked) {
        this.$cookies.set(this.cookieName, `y`, {
          expires: new Date(
            new Date().setFullYear(new Date().getFullYear() + 1),
          ),
        });
      } else {
        this.$cookies.set(this.cookieName, `y`);
      }
      this.$emit(`guide-cover-close`);
    },
  },
};
</script>
<style lang="scss" module src="./index.scss"></style>
