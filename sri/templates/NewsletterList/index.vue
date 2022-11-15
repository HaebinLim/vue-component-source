<template>
  <MonthlyNewsletter
    :current-year="currentYear"
    :years="years"
    :list-data="listData"
    :class="$style[`newsletter-list`]"
  />
</template>
<script>
import MonthlyNewsletter from '@organisms/MonthlyNewsletter';
export default {
  components: {
    MonthlyNewsletter,
  },
  props: {
    /**
     * 현재 연도
     */
    currentYear: {
      type: String,
      required: true,
      default: ``,
    },
    /**
     * 발송한 연도 모음
     */
    years: {
      type: Array,
      required: false,
      default: () => {
        return [];
      },
    },
    /**
     * 월별 메일 목록 Data
     */
    listData: {
      type: Object,
      required: true,
      default: () => {
        return {};
      },
    },
  },
};
</script>
<style lang="scss" module src="./index.scss"></style>
