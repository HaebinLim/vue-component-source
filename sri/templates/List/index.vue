<template>
  <div :class="$style[`item-list`]">
    <template v-if="isNoContent">
      <no-content
        :page-title="noContentTitle"
        :page-desc="noContentDesc"
        :btn-link="listType === `archived` ? `/` : ``"
        :btn-text="listType === `archived` ? `콘텐츠 구경가기` : ``"
        :tag-data="listType !== `archived` ? noContentTagData : []"
      />
    </template>
    <template v-else>
      <plain-list
        :heading-rank="1"
        :list-type="listType"
        :query="query"
        :editor-meta="editorMeta"
        :banner-data="bannerData"
        :list-data="listData"
        :total-count="totalCount"
        :sort="sort"
        :sort-options="sortOptions"
        :show-more="showMore"
        :pagination-option="paginationOption"
        :update-archive="updateArchive"
        :collect-activity="collectActivity"
        @change-sorting="handleChangeSort"
        @change-archive="handleChangeArchive"
        @changed-archive="handleChangedArchive"
        @click-more="handleClickMore"
        @delete-selected="handleDeleteSelected"
      />
    </template>
  </div>
</template>

<script>
import PlainList from '@organisms/PlainList';
import NoContent from '@organisms/NoContent';
export default {
  components: {
    PlainList,
    NoContent,
  },
  props: {
    /**
     * 결과 없음 페이지인지
     */
    isNoContent: {
      type: Boolean,
      required: false,
      default: false,
    },
    /**
     * 결과 없음 페이지에서 사용할 태그 리스트
     */
    noContentTagData: {
      type: Array,
      required: false,
      default: () => {
        return [];
      },
    },
    /**
     * 목록 종류
     */
    listType: {
      type: String,
      required: false,
      default: `related`,
      validator(ctx) {
        return [`archived`, `search`, `tag`, `editor`].includes(ctx);
      },
    },
    /**
     * 목록 조회 기준
     */
    query: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * 작성글 리스트일경우 작성자 정보
     */
    editorMeta: {
      type: Object,
      required: false,
      default: () => {
        return {};
      },
    },
    /**
     * API에서 받아온 배너 데이터
     */
    bannerData: {
      type: Object,
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
     * 전체 글 갯수
     */
    totalCount: {
      type: Number,
      required: false,
      default: 0,
    },
    /**
     * 정렬 기본값
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
     * MORE 버튼 노출 여부
     */
    showMore: {
      type: Boolean,
      required: false,
      default: false,
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
  computed: {
    noContentTitle() {
      switch (this.listType) {
        case `archived`:
          return `담은콘텐츠`;
        case `editor`:
          return `${this.editorMeta.name}의 작성글`;
        default:
          return this.query;
      }
    },
    noContentDesc() {
      switch (this.listType) {
        case `archived`:
          return `아직 담은 콘텐츠가 없네요\n콘텐츠를 담아주세요`;
        default:
          return `찾으시는 내용이 없습니다.\n다른 검색어를 이용해주세요`;
      }
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
    handleDeleteSelected(paylaod) {
      /**
       * 선택 삭제 버튼 클릭시 발생 이벤트 ()
       *
       * @event change-archive
       */
      this.$emit('delete-selected', paylaod);
    },
  },
};
</script>
<style lang="scss" module src="./index.scss"></style>
