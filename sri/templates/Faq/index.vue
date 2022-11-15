<template>
  <FaqList :list-data="listData" :pagination-option="paginationOption" />
</template>
<script>
import FaqList from '@organisms/FaqList';
export default {
  components: {
    FaqList,
  },
  props: {
    /**
     * FAQ 리스트 데이터
     */
    listData: {
      type: Array,
      required: true,
      default: () => [],
    },
    /**
     * Pagination 사용시 전달할 옵션
     */
    paginationOption: {
      type: Object,
      required: false,
      default: () => {
        return {};
      },
    },
  },
};
</script>
<style lang="scss" module src="./index.scss"></style>
