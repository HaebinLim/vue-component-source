<template>
  <div
    :class="[
      $style[`reply`],
      { [$style[`reply--in-cut`]]: inCut },
      { grid: !inCut },
    ]"
  >
    <div
      :class="[
        $style[`reply__header`],
        { [$style[`reply__header--in-cut`]]: inCut },
      ]"
    >
      <heading :rank="headingRank" :class="[$style[`reply__heading`]]">
        {{ headingTitle }}
        <span :class="[$style[`reply__heading__count`]]">
          {{ count }} <span :class="$style[`--sr-only`]">건</span>
        </span>
      </heading>
      <SelectBox
        :type="`basic`"
        :label="`정렬`"
        :show-label="false"
        :value="sort"
        :options="sortingOption"
        :disabled="!totalCount"
        @change="changeSort"
      />
    </div>
    <div
      :class="[
        $style[`reply__list__container`],
        { [$style[`reply__list__container--in-cut-all`]]: inCutAll },
      ]"
    >
      <heading :rank="headingRank + 1" :is-hidden="true"> Best 댓글 </heading>
      <ReplyList
        v-if="bestReplies.length"
        :replies="bestReplies"
        :is-best-replies="true"
        :user-id="userId"
        :in-cut="inCut"
        :show-reply-write-form="showReplyWriteForm"
        :update-like="updateLike"
        :update-dislike="updateDislike"
        :create-reply="createReply"
        :update-reply="updateReply"
        :class="[$style[`reply__list`], $style[`reply__list--best`]]"
        @click-report="handleClickReport"
        @click-author="handleClickAuthor"
        @click-delete="handleClickDelete"
        @click-origin="handleClickOrigin"
        @click-go-cut="handleGoCut"
        @enter-modify-mode="handleEnterModify"
        @success-modify="handleLeaveModify"
        @fail-modify="handleLeaveModify"
      />
      <heading :rank="headingRank + 1" :is-hidden="true"> 댓글 </heading>
      <ReplyList
        v-if="totalCount"
        :replies="replies"
        :is-best-replies="false"
        :user-id="userId"
        :in-cut="inCut"
        :show-reply-write-form="showReplyWriteForm"
        :update-like="updateLike"
        :update-dislike="updateDislike"
        :create-reply="createReply"
        :update-reply="updateReply"
        :class="[$style[`reply__list`]]"
        @click-report="handleClickReport"
        @click-author="handleClickAuthor"
        @click-delete="handleClickDelete"
        @created-reply="handleCreatedReply"
        @click-go-cut="handleGoCut"
        @fail-reply="handleFailReply"
        @enter-modify-mode="handleEnterModify"
        @success-modify="handleLeaveModify"
        @fail-modify="handleLeaveModify"
        @enter-write-rereply-mode="handleEnterModify"
      />
      <template v-else>
        <div :class="$style[`reply__list--empty`]">
          해당 게시물에 댓글이 없습니다.
        </div>
      </template>
      <template v-if="totalCount">
        <div :class="$style[`reply__pagination`]">
          <Pagination
            :parameter-name="pageParamName"
            :query="routerQuery"
            :router-path="routerPath"
            :visible-pages="10"
            :current-page="currPage"
            :total-pages="totalPage"
          />
        </div>
      </template>
    </div>
    <div
      ref="writeContainer"
      :class="[
        $style[`reply__write`],
        { [$style[`reply__write--in-cut`]]: inCut },
      ]"
    >
      <ReplyWriteForm
        v-show="!showReplyWriteForm"
        :is-logged-in="!!userId"
        :user-name="userName"
        :create-reply="createReply"
        :update-reply="updateReply"
        :use-compact-mode="inCut"
        @submit-succed="handleCreatedReply"
        @submit-failed="handleFailReply"
      />
      <PortalTarget ref="replyWriteForm" name="ReplyWriteForm" />
    </div>
    <Report
      v-if="reportedComment"
      :heading-rank="headingRank + 2"
      :target-id="reportedComment"
      :submit-report="submitReport"
      :is-logged-in="!!userId"
      @closed="handleCloseReport"
    />
    <Profile
      v-if="authorProfile"
      :heading-rank="headingRank + 2"
      :author-id="authorProfile.authorId"
      :author-name="authorProfile.authorName"
      :total-replies="authorProfile.totalReplies"
      :delete-replies="authorProfile.deleteReplies"
      :deleted-replies="authorProfile.deletedReplies"
      @closed="handleCloseProfile"
    />
  </div>
</template>

<script>
import ReplyList from '@molecules/ReplyList';
import Heading from '@atoms/Heading/index';
import SelectBox from '@atoms/SelectBox/index';
import Pagination from '@molecules/Pagination';
import Report from '@organisms/Report';
import Profile from '@organisms/Profile';
import { toStringMaxedNumber } from '@/plugins/utils';
import ReplyWriteForm from '@molecules/ReplyWriteForm/index';

export default {
  components: {
    ReplyList,
    Heading,
    SelectBox,
    Pagination,
    ReplyWriteForm,
    Report,
    Profile,
  },
  props: {
    /**
     * 베스트 댓글 목록
     */
    bestReplies: {
      type: Array,
      required: true,
      default: () => [],
    },
    /**
     * 댓글 목록
     */
    replies: {
      type: Array,
      required: true,
      default: () => [],
    },
    /**
     * 댓글 총 개수
     */
    totalCount: {
      type: Number,
      required: true,
      default: 0,
    },
    /**
     * 로그인 유저 아이디
     */
    userId: {
      type: [String, Number],
      required: false,
      default: ``,
    },
    /**
     * 로그인 유저 이름
     */
    userName: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * 카드 뉴스 컷당 댓글 여부
     */
    inCut: {
      type: Boolean,
      required: false,
      default: false,
    },
    /**
     * 카드 뉴스 컷당 댓글 ...이면서 전체 댓글일때.....
     */
    inCutAll: {
      type: Boolean,
      required: false,
      default: false,
    },
    /**
     * 댓글 정렬 기준
     */ sort: {
      type: [String, Number],
      required: false,
      default: `recent`,
    },
    /**
     * 댓글 수정 API 함수
     */
    updateReply: {
      type: Function,
      required: true,
    },
    /**
     * (대)댓글 작성 API 함수
     */
    createReply: {
      type: Function,
      required: true,
    },
    /**
     * 좋아요 설정/해제 처리 API 함수
     */
    updateLike: {
      type: Function,
      required: true,
    },
    /**
     * 싫어요 설정/해제 처리 API 함수
     */
    updateDislike: {
      type: Function,
      required: true,
    },
    /**
     * 영역 헤딩 랭크
     */
    headingRank: {
      type: Number,
      required: true,
      default: 2,
    },
    /**
     * 댓글 영역 헤딩 텍스트
     */
    headingTitle: {
      type: String,
      required: false,
      default: `Reply`,
    },
    /**
     * 댓글 페이지(라우터) 이동 방식
     */
    pageParamType: {
      type: String,
      required: false,
      default: `params`,
    },
    /**
     * `parameterType`에 사용된 이름
     */
    pageParamName: {
      type: String,
      required: false,
      default: `page`,
    },
    /**
     * router 이름 nuxt-link path
     */
    routerPath: {
      type: String,
      required: true,
      default: ``,
    },
    /**
     * Router 변경 시 필수로 포함되어야 할 URLParam (QueryString)
     */
    routerQuery: {
      type: [Object, null],
      require: false,
      default: null,
    },
    /**
     * 현재 페이지
     */
    currPage: {
      type: Number,
      required: true,
      default: 1,
    },
    /**
     * 마지막 페이지
     */
    totalPage: {
      type: Number,
      required: true,
      default: 1,
    },
    /**
     * 신고 API 함수
     */
    submitReport: {
      type: Function,
      required: true,
    },
    /**
     * 유저 정보 API 함수
     */
    getAuthorProfile: {
      type: Function,
      required: true,
    },
  },
  data() {
    return {
      reportedComment: null,
      authorProfile: null,
      showReplyWriteForm: false,
    };
  },
  computed: {
    count() {
      return toStringMaxedNumber(this.totalCount, 9999);
    },
    sortingOption() {
      return [
        {
          text: `최신순`,
          value: `grpRegDtm,desc`,
        },
        {
          text: `추천수`,
          value: `grpRecommendCnt,desc`,
        },
        {
          text: `순추천수`,
          value: `grpPopularity,desc`,
        },
      ];
    },
  },
  methods: {
    changeSort(payload) {
      /**
       * 댓글 정렬 순서 변경 <br>
       * `any` (selected value)
       * @event change-sort
       */
      this.$emit(`change-sort`, payload);
    },
    handleClickReport({ id }) {
      this.reportedComment = id;
    },
    handleCloseReport() {
      this.reportedComment = null;
    },
    handleClickAuthor({ authorId, authorName }) {
      const { getAuthorProfile } = this;
      getAuthorProfile(authorId)
        .then(authorData => {
          this.authorProfile = { ...authorData, authorId, authorName };
        })
        .catch(error => {
          const msg = error?.detail;
          this.authorProfile = null;
          alert(msg);
        });
    },
    handleCloseProfile() {
      this.authorProfile = null;
    },
    handleClickDelete(payload) {
      /**
       * 삭제 버튼 누를 시 발생 <br>
       * `{ id }`
       * @event click-delete
       */
      this.$emit(`click-delete`, payload);
    },
    handleClickOrigin(payload) {
      /**
       * 원문보기 버튼 누를 시 발생 <br>
       * `{ id }`
       * @event click-origin
       */
      this.$emit(`click-origin`, payload);
    },
    handleGoCut(payload) {
      /**
       * 원문보기 버튼 누를 시 발생 <br>
       * `{ cutId }`
       * @event click-go-cut
       */
      this.$emit(`click-go-cut`, payload);
    },
    handleCreatedReply() {
      /**
       * (대)댓글 작성 완료 시 발생
       * @event created-reply
       */
      this.$emit(`created-reply`);
      this.showReplyWriteForm = false;
    },
    handleFailModify() {
      /**
       * 댓글 수정 실패 시 발생
       * @event fail-modify
       */
      this.$emit(`fail-modify`);
    },
    handleFailReply() {
      /**
       * (대)댓글 작정 실패 시 발생
       * @event fail-reply
       */
      this.$emit(`fail-reply`);
    },
    handleEnterModify() {
      this.showReplyWriteForm = true;
      if (this.inCut) {
        setTimeout(() => {
          document.addEventListener(`click`, this.bindClosingPortal, false);
        }, 0);
      }
    },
    handleLeaveModify() {
      this.showReplyWriteForm = false;
    },
    bindClosingPortal(event) {
      const formContainer = this.$refs.replyWriteForm.$el;
      const prevented = event.path.includes(formContainer);

      if (!prevented) {
        this.showReplyWriteForm = false;
        document.removeEventListener(`click`, this.bindClosingPortal, false);
      }
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
