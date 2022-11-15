<template>
  <div :class="$style[`card-viewer`]">
    <div :class="$style[`card-viewer__frame`]">
      <GuideCover
        v-if="showGuideCover"
        :cover-title="`화면을 좌우로\n슬라이드하면\n페이지가 이동됩니다.`"
        :cover-desc="`화면을 좌우로 터치하셔도 됩니다.`"
        :cookie-id="`card_news`"
        :class="$style[`card-viewer__guide`]"
        @guide-cover-close="handleGuideCoverClose"
      />
      <ViewerHeader
        :title="atricleTitle"
        :class="$style[`card-viewer__header`]"
        @click-close="handleViewerClose"
      />
      <!-- 내용 -->
      <div :class="$style[`card-viewer__content`]">
        <flicking
          ref="carousel"
          :options="carouselOptions"
          :class="$style[`card-viewer__card`]"
          @changed="handleCutChange"
          @ready="handleCarouselReady"
        >
          <template
            v-for="{ cardNo: id, imagePath: url, cardExln: alt } in cardData"
          >
            <div :key="id" :class="$style[`card-viewer__card__item`]">
              <img :src="$resolveImageUrl(url)" :alt="alt" />
            </div>
          </template>
          <div
            :class="[
              $style[`card-viewer__card__item`],
              $style[`card-viewer__card__item--all`],
              `grid`,
            ]"
          >
            <PostHashtags
              :hashtags="hashtagData"
              :heading-rank="4"
              :class="$style[`card-viewer__hashtag`]"
            />
            <PostWriter
              :type="`card`"
              :heading-rank="3"
              :editor="editorData"
              :class="$style[`card-viewer__writer`]"
            />
          </div>
        </flicking>
        <div :class="$style[`card-viewer__controls`]">
          <div :class="$style[`card-viewer__pagination`]">
            <template v-for="(item, idx) in cardData">
              <span
                :key="idx"
                :class="[
                  $style[`card-viewer__pagination__dot`],
                  {
                    [$style[`card-viewer__pagination__dot--active`]]:
                      currIdx === idx,
                  },
                ]"
                :aria-label="`${idx + 1} 페이지`"
              ></span>
            </template>
            <span
              :class="[
                $style[`card-viewer__pagination__dot`],
                {
                  [$style[`card-viewer__pagination__dot--active`]]: isLastCut,
                },
              ]"
              :aria-label="`${maxIdx + 1} 페이지`"
            ></span>
          </div>
          <div
            v-show="isLastCut"
            :class="[
              $style[`card-viewer__btn-wrap`],
              $style[`card-viewer__btn-wrap--navi`],
            ]"
          >
            <LinkText
              v-if="prevArticle"
              :type="`button`"
              :theme="`dark`"
              :appearance="`grey`"
              :label="`PREV`"
              :to="`/post/${prevArticle.id}`"
              :class="$style[`card-viewer__btn--prev`]"
            />
            <LinkText
              v-if="nextArticle"
              :type="`button`"
              :theme="`dark`"
              :appearance="`grey`"
              :label="`NEXT`"
              :to="`/post/${nextArticle.id}`"
              :class="$style[`card-viewer__btn--next`]"
            />
          </div>
          <div
            v-show="!isLastCut"
            :class="[
              $style[`card-viewer__btn-wrap`],
              $style[`card-viewer__btn-wrap--card`],
            ]"
          >
            <BoxButton
              :type="`button`"
              :appearance="`arrow-left`"
              :theme="`dark`"
              :size="`large`"
              :label="`이전 카드`"
              :class="[$style[`card-viewer__btn--prev`], `flicking-arrow-prev`]"
            />
            <BoxButton
              :type="`button`"
              :appearance="`arrow-right`"
              :theme="`dark`"
              :size="`large`"
              :label="`다음 카드`"
              :class="[$style[`card-viewer__btn--next`], `flicking-arrow-next`]"
            />
          </div>
        </div>
      </div>

      <ViewerFooter
        :post-id="postId"
        :replies="replyListData.totalCount"
        :likes="likes"
        :archives="archives"
        :shares="shares"
        :liked="liked"
        :archived="archived"
        :update-like="updateArticleLike"
        :update-archive="updateArticleArchive"
        :open-reply="openReply"
        :class="$style[`card-viewer__footer`]"
        @click-reply="handleClickOpenReply"
        @click-like="handleClickLikePost"
        @click-archive="handleClickArchivePost"
        @click-share="handleClickShare"
      />
    </div>
    <div
      :class="[
        $style[`card-viewer__reply-wrap`],
        { [$style[`card-viewer__reply-wrap--show`]]: showReply },
      ]"
    >
      <ViewerHeader
        :title="isLastCut ? `전체 댓글` : `페이지 댓글`"
        :class="$style[`card-viewer__reply__header`]"
        @click-close="handleReplyClose"
      />
      <Reply
        ref="reply"
        :best-replies="replyListData.best"
        :replies="replyListData.list"
        :total-count="replyListData.totalCount"
        :curr-page="replyListData.currentPage"
        :total-page="replyListData.lastPage"
        :user-id="(userInfo && userInfo.memNo) || ``"
        :user-name="(userInfo && userInfo.memNick) || ``"
        :sort="replyListData.order"
        :in-cut="true"
        :in-cut-all="isLastCut"
        :heading-rank="2"
        :heading-title="isLastCut ? `Reply` : `댓글`"
        :update-like="updateReplyLike"
        :update-dislike="updateReplyDislike"
        :create-reply="createReply"
        :update-reply="updateReply"
        :submit-report="submitReport"
        :get-author-profile="getAuthorProfile"
        :page-param-name="replyPagingKey"
        :router-path="replyRoutePath"
        :router-query="replyRouteQuery"
        :class="$style[`card-viewer__reply__body`]"
        @change-sort="handleChangeReplySort"
        @click-delete="handleDeleteReply"
        @click-origin="handleClickOrigin"
        @click-go-cut="handleGoCut"
        @created-reply="handleCreatdReply"
      />
    </div>
  </div>
</template>

<script>
import LinkText from '@atoms/Link/Text/index';
import BoxButton from '@atoms/Button/Box/index';
import ViewerHeader from '@organisms/ViewerHeader/index';
import ViewerFooter from '@organisms/ViewerFooter/index';
import GuideCover from '@organisms/GuideCover/index';
import PostHashtags from '@organisms/PostHashtags/index';
import PostWriter from '@organisms/PostWriter/index';
import Reply from '@organisms/Reply/index';
import findIndex from 'lodash/findIndex';
import Easing from 'easing-functions';
import { Flicking } from '@egjs/vue-flicking';
import { Arrow } from '@egjs/flicking-plugins';
require('@egjs/vue-flicking/dist/flicking.css');
export default {
  components: {
    LinkText,
    BoxButton,
    ViewerHeader,
    ViewerFooter,
    GuideCover,
    PostHashtags,
    PostWriter,
    Reply,
    Flicking,
  },
  props: {
    /**
     * 사용자 정보
     */
    userInfo: {
      type: [Object],
      required: false,
      default: () => ({}),
    },
    /**
     * 포스트 ID
     */
    postId: {
      type: [String, Number],
      required: true,
      default: ``,
    },
    /**
     * 카드 데이터
     */
    cardData: {
      type: Array,
      required: true,
      default: () => [],
    },
    /**
     * 카드 뉴스 초기 인덱스 값
     */
    initCardIdx: {
      type: [Number, null],
      required: false,
      default: null,
    },
    /**
     * 해시태그 데이터
     */
    hashtagData: {
      type: Array,
      required: true,
      default: () => [],
    },
    /**
     * editor 데이터
     */
    editorData: {
      type: Object,
      required: true,
      default: () => {},
    },
    /**
     * 댓글 데이터
     */
    repliesData: {
      type: Object,
      required: true,
      default: () => {},
    },
    /**
     * 댓글 PAgination 에 전달할 router 주소
     */
    replyRoutePath: {
      type: String,
      required: true,
      default: ``,
    },
    /**
     * Router 변경 시 필수로 포함되어야 할 URLParam (QueryString)
     */
    replyRouteQuery: {
      type: [Object, null],
      required: false,
      default: null,
    },
    /**
     * 댓글 페이징 데이터 식별자
     */
    replyPagingKey: {
      type: String,
      required: true,
      default: `reply`,
    },
    /**
     * 댓글 수정 API 함수
     */
    updateReply: {
      type: Function,
      required: true,
    },
    /**
     * 대댓글 작성 API 함수
     */
    createReply: {
      type: Function,
      required: true,
    },
    /**
     * 댓글 좋아요 설정/해제 처리 API 함수
     */
    updateReplyLike: {
      type: Function,
      required: true,
    },
    /**
     * 댓글 싫어요 설정/해제 처리 API 함수
     */
    updateReplyDislike: {
      type: Function,
      required: true,
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
    /**
     * 아티클 제목
     */
    atricleTitle: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * 하단 좋아요 수
     */
    likes: {
      type: Number,
      required: false,
      default: 0,
    },
    /**
     * 하단 담기 수
     */
    archives: {
      type: Number,
      required: false,
      default: 0,
    },
    /**
     * 하단 공유 수
     */
    shares: {
      type: Number,
      required: false,
      default: 0,
    },
    /**
     * 하단 좋아요 상태
     */
    liked: {
      type: Boolean,
      required: false,
      default: false,
    },
    /**
     * 하단 담기 상태
     */
    archived: {
      type: Boolean,
      required: false,
      default: false,
    },
    /**
     * 하단 좋아요 설정/해제 처리 API 함수
     */
    updateArticleLike: {
      type: Function,
      required: true,
    },
    /**
     * 하단 담기 설정/해제 처리 API 함수
     */
    updateArticleArchive: {
      type: Function,
      required: true,
    },
    /**
     * 하단 이전글 정보
     */
    prevArticle: {
      type: Object,
      required: false,
      default: () => ({}),
    },
    /**
     * 하단 다음글 정보
     */
    nextArticle: {
      type: Object,
      required: false,
      default: () => ({}),
    },
    /**
     * 댓글 열린 상태
     */
    openReply: {
      type: Boolean,
      required: false,
      default: false,
    },
  },
  data() {
    return {
      replyListData: {},
      showGuideCover: true,
      showReply: false,
      currIdx: this.initCardIdx || 0,
      carouselOptions: {
        resizeOnContentsReady: true,
        autoResize: true,
        duration: 500,
        bounce: 0,
        align: `prev`,
        easing: Easing.Quadratic.In,
        noPanelStyleOverride: true,
        defaultIndex: this.initCardIdx || 0,
      },
      arrowPlugin: null,
    };
  },
  computed: {
    maxIdx() {
      return this.cardData.length;
    },
    isLastCut() {
      return this.currIdx === this.maxIdx;
    },
  },
  watch: {
    repliesData(data) {
      this.replyListData = { ...data };
    },
    initCardIdx(idx) {
      this.$refs.carousel.moveTo(idx);
    },
  },
  mounted() {
    this.arrowPlugin = new Arrow({ parentEl: this.$parent.$el });
    this.$nextTick(() => {
      this.$refs.carousel.addPlugins(this.arrowPlugin);
    });
  },
  created() {
    this.replyListData = { ...this.repliesData };
    if (this.$cookies.get(`hide_cover_card_news`) === `y`) {
      this.showGuideCover = false;
    }
    if (this.openReply) {
      this.handleClickOpenReply();
    }
  },
  destroyed() {
    window.removeEventListener('keydown', this.handleArrowKey);
  },
  methods: {
    handleGuideCoverClose() {
      this.showGuideCover = false;
    },
    handleClickOpenReply() {
      this.showReply = true;
    },
    handleClickLikePost() {
      this.updateArticleLike(!this.pressLike)
        .then(({ likes, liked }) => {
          this.likesCount = likes;
          this.pressLike = liked;
        })
        .catch(error => {
          const { message } = error;
          alert(this.$msg(message));
        });
    },
    handleClickArchivePost() {
      this.updateArticleArchive(!this.pressArchive)
        .then(({ archives, archived }) => {
          this.archivesCount = archives;
          this.pressArchive = archived;
        })
        .catch(error => {
          const { message } = error;
          alert(this.$msg(message));
        });
    },
    handleClickShare() {
      /**
       * 공유 클릭시 발생 이벤트
       *
       * @event click-share
       *
       */
      this.$emit(`click-share`);
    },
    prevCard() {
      if (this.currIdx === 0) return;
      this.arrowPlugin._onPrevClick();
    },
    nextCard() {
      if (this.currIdx >= this.maxIdx) return;
      this.arrowPlugin._onNextClick();
    },
    handleCutChange(event) {
      const { index } = event;
      this.currIdx = index;
      /**
       * 이미지컷 변경시 발생(답글리스트 갱신)
       *
       * @event update-reply-list
       */
      this.$emit(`changed-cut`, this.currIdx);
    },
    handleViewerClose() {
      /**
       * ViewerHeader 에서 닫기버튼 클릭시 발생 이벤트 (모바일)
       *
       * @event close-article-view
       */
      this.$emit(`close-article-view`);
    },
    handleReplyClose() {
      this.showReply = false;
    },
    handleArrowKey(event) {
      this.$nextTick(() => {
        const writeForm = this.$refs.reply.$refs.writeContainer;
        const prevented = this.showGuideCover || event.path.includes(writeForm);
        if (!prevented) {
          this.keyPressed = true;
          if (event.keyCode === 37) {
            this.prevCard();
          }
          if (event.keyCode === 39) {
            this.nextCard();
          }
        }
      });
    },
    handleCarouselReady() {
      window.addEventListener('keydown', this.handleArrowKey);
    },
    updateReplyList(cutIdx, replyPage) {
      this.getReplyList(cutIdx, replyPage)
        .then(list => {
          this.replyListData = list;
        })
        .catch(error => {
          alert(error);
        });
    },
    handleChangeReplySort(sort) {
      /**
       * 댓글 정렬 순서 변경 <br>
       * `any` (selected value)
       * @event change-reply-sort
       */
      this.$emit(`change-reply-sort`, { sort, cut: this.currIdx });
    },
    handleDeleteReply(payload) {
      /**
       * 댓글 삭제 버튼 누를 시 발생 <br>
       * `{ id }`
       * @event delete-reply
       */
      this.$emit(`delete-reply`, payload);
    },
    handleClickOrigin(payload) {
      /**
       * 댓글 원문보기 버튼 누를 시 발생 <br>
       * `{ id }`
       * @event click-origin
       */
      this.$emit(`click-reply-origin`, payload);
    },
    handleCreatdReply() {
      /**
       * (대)댓글 작성(수정) 완료 시 발생
       * @event created-reply
       */
      this.$emit(`created-reply`);
    },
    handleGoCut({ cutId }) {
      const cardIndex = findIndex(this.cardData, card => card.cardNo === cutId);

      /**
       * 이미지컷 변경시 발생(답글리스트 갱신)
       *
       * @event update-reply-list
       */
      this.$emit(`click-go-cut`, cardIndex);
    },
  },
};
</script>
<style lang="scss" module src="./index.scss"></style>
