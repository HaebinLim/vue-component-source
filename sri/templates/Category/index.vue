<template>
  <div :class="$style[`category-wrap`]">
    <CategoryBestTop
      v-if="categoryBestData"
      :list-data="categoryBestData"
      :list-category="listCategory"
      :update-archive="updateArchive"
      @change-archive="handleChangeArchive"
      @changed-archive="handleChangedArchive"
    />
    <div :class="$style[`category-wrap__bg`]">
      <CategoryList
        :list-category="listCategory"
        :banner-data="bannerData"
        :list-data="listData"
        :total-count="totalCount"
        :sort="sort"
        :sort-options="sortOptions"
        :show-more="showMore"
        :update-archive="updateArchive"
        :collect-activity="collectActivity"
        @change-sorting="handleChangeSort"
        @change-archive="handleChangeArchive"
        @changed-archive="handleChangedArchive"
        @click-more="handleClickMore"
      />
      <div v-if="bottomBannerData" :class="`grid`">
        <Banner
          v-bind="bottomBannerData"
          :stretched="true"
          :collect-activity="collectActivity"
        />
      </div>
    </div>
  </div>
</template>

<script>
import CategoryBestTop from '@organisms/CategoryBestTop';
import CategoryList from '@organisms/CategoryList';
import Banner from '@molecules/Banner';
export default {
  components: {
    CategoryBestTop,
    CategoryList,
    Banner,
  },
  props: {
    /**
     *  목록 카테고리
     */
    listCategory: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * 현재 사용하고 있는 정렬값
     */
    sort: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * 정렬 옵션
     */
    sortOptions: {
      type: Array,
      required: false,
      default: () => {
        return [];
      },
    },
    /**
     * API에서 받아온 배너 데이터 (리스트중간배너)
     */
    bannerData: {
      type: [Object, null],
      required: false,
      default: () => {
        return {};
      },
    },
    /**
     * API에서 받아온 배너 데이터 (하단배너)
     */
    bottomBannerData: {
      type: [Object, null],
      required: false,
      default: () => {
        return {};
      },
    },
    /**
     * API에서 받아온 리스트 데이터
     */
    listData: {
      type: Array,
      required: true,
      default: () => {
        return [];
      },
    },
    /**
     * API에서 받아온 카테고리베스트 데이터
     */
    categoryBestData: {
      type: [Array, null],
      required: false,
      default: () => {
        return [];
      },
    },
    /**
     * 전체 글 갯수
     */
    totalCount: {
      type: Number,
      required: false,
      default: 0,
    },
    /**
     * MORE 버튼 노출 여부
     */
    showMore: {
      type: Boolean,
      required: false,
      default: false,
    },
    /**
     * 담기 설정/해제 처리 API 함수
     */
    updateArchive: {
      type: Function,
      required: false,
      default: undefined,
    },
    /**
     * 활동내역 수집 처리 API 함수
     */
    collectActivity: {
      type: Function,
      required: false,
      default: undefined,
    },
  },
  methods: {
    handleChangeSort(val) {
      /**
       * 정렬옵션 변경시 발생 이벤트
       *
       * @event change-sorting
       */
      this.$emit('change-sorting', val);
    },
    handleClickMore() {
      /**
       * MORE버튼 클릭시 발생 이벤트
       *
       * @event click-more
       */
      this.$emit('click-more');
    },
    handleChangedArchive(payload) {
      /**
       * 콘텐츠 담기 상태 변경 시 발생
       * @event changed-archive
       */
      this.$emit('changed-archive', payload);
    },
    handleChangeArchive(paylaod) {
      /**
       * 콘텐츠 담기 버튼 클릭 시 발생 <br>
       * `{id, state}`
       * @event change-archive
       */
      this.$emit('change-archive', paylaod);
    },
  },
};
</script>
<style lang="scss" module src="./index.scss"></style>
