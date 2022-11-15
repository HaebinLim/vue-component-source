<template>
  <div :class="$style[`header`]">
    <span :class="$style[`header__title`]">
      {{ title }}
    </span>
    <BoxButton
      :type="`button`"
      :appearance="`icon`"
      :size="`large`"
      :label="`닫기`"
      :theme="`dark`"
      :icon="`close`"
      :class="$style[`header__close`]"
      @click="handleClickClose"
    />
  </div>
</template>

<script>
import BoxButton from '@atoms/Button/Box/index';

export default {
  components: {
    BoxButton,
  },
  props: {
    /**
     * 아티클 제목
     */
    title: {
      type: String,
      required: true,
      default: ``,
    },
  },
  methods: {
    handleClickClose() {
      /**
       * 닫기 버튼 클릭시 발생 이벤트
       *
       * @event click-close
       */
      this.$emit(`click-close`);
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
