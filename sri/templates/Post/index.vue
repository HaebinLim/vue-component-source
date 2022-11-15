<template>
  <section :class="$style[`post`]">
    <ReadingBar
      v-if="postInfo.postType === `article`"
      :progress="readingRate"
      :class="[$style[`post__reading-bar`]]"
    />
    <div :class="[`grid`, $style[`post__header`]]">
      <PostHeader
        :post-id="postId"
        :post-type="postInfo.postType"
        :category="postInfo.category"
        :title="postInfo.title"
        :last-modified="
          $dayjs(postInfo.lastModified).format('YYYY.MM.DD HH:mm')
        "
        :read-time="
          $dayjs.duration(postInfo.readingTime, 's').format('m분 s초')
        "
        :likes.sync="postInfo.likes"
        :liked.sync="postInfo.liked"
        :archives.sync="postInfo.archives"
        :archived.sync="postInfo.archived"
        :shares="postInfo.shares"
        :update-like="updateLikePost"
        :update-archive="updateArchivePost"
        :class="[
          {
            [$style[`card-viewer--mobile__header`]]:
              isCardOnMobile && isFirstCut,
          },
        ]"
        @click-share="handleClickShare"
      />
    </div>
    <template v-if="isArticleType">
      <component
        :is="ArticleTemplate"
        v-bind="ArticleProps"
        ref="article"
        :article-type="postInfo.postType"
      />
      <div :class="[`grid`, $style[`article__utils`]]">
        <PostUtil
          :post-id="postId"
          :likes.sync="postInfo.likes"
          :liked.sync="postInfo.liked"
          :archives.sync="postInfo.archives"
          :archived.sync="postInfo.archived"
          :shares="postInfo.shares"
          :update-like="updateLikePost"
          :update-archive="updateArchivePost"
          @click-share="handleClickShare"
        />
      </div>
      <section :class="[`grid`, $style[`article__tags`]]">
        <PostHashtags :heading-rank="2" :hashtags="postInfo.hashTags" />
      </section>
      <section :class="[`grid`, $style[`article__editor`]]">
        <PostWriter :heading-rank="2" :editor="postInfo.editor" />
      </section>
      <section :class="$style[`article__reply`]">
        <Reply
          id="reply"
          :best-replies="repliesInfo.best"
          :replies="repliesInfo.list"
          :total-count="repliesInfo.totalCount"
          :curr-page="repliesInfo.currentPage"
          :total-page="repliesInfo.lastPage"
          :user-id="(user && user.memNo) || ``"
          :user-name="(user && user.memNick) || ``"
          :sort="repliesInfo.order"
          :heading-rank="2"
          :heading-title="`Reply`"
          :update-like="updateLikeReply"
          :update-dislike="updateDislikeReply"
          :create-reply="createReply"
          :update-reply="updateReply"
          :page-param-name="replyPagingKey"
          :router-path="routerPath"
          :router-query="routerQuery"
          :submit-report="submitReport"
          :get-author-profile="getReplyAuthorProfile"
          @change-sort="handleChangeSort"
          @click-delete="handleDeleteReply"
          @click-origin="handleClickOrigin"
          @created-reply="handleCreatdReply"
        />
      </section>
    </template>
    <template v-if="isCardType">
      <component
        :is="cardTemplate"
        :post-id="postId"
        :card-data="postInfo.contents"
        :init-card-idx="initCardIdx"
        :likes="postInfo.likes"
        :liked="postInfo.liked"
        :archives="postInfo.archives"
        :archived="postInfo.archived"
        :shares="postInfo.shares"
        :update-article-like="updateLikePost"
        :update-article-archive="updateArchivePost"
        :hashtag-data="postInfo.hashTags"
        :editor-data="postInfo.editor"
        :prev-article="postInfo.prev"
        :next-article="postInfo.next"
        :atricle-title="postInfo.title"
        :replies-data="repliesInfo"
        :reply-route-path="routerPath"
        :reply-route-query="routerQuery"
        :reply-paging-key="replyPagingKey"
        :update-reply-like="updateLikeReply"
        :update-reply-dislike="updateDislikeReply"
        :create-reply="createReply"
        :update-reply="updateReply"
        :submit-report="submitReport"
        :get-author-profile="getReplyAuthorProfile"
        :user-info="$auth.user || {}"
        :open-reply="openReply"
        :class="[
          $style[`card-viewer`],
          { [$style[`card-viewer--mobile`]]: isCardOnMobile },
        ]"
        @click-share="handleClickShare"
        @changed-cut="handleChangedCut"
        @change-reply-sort="handleChangeSort"
        @delete-reply="handleDeleteReply"
        @created-reply="handleCreatdReply"
        @click-reply-origin="handleClickOrigin"
        @click-go-cut="handleChangedCut"
        @close-article-view="handleCloseCardViewer"
      />
    </template>
    <aside
      v-if="bannerInfo"
      v-show="!isCardOnMobile"
      :class="[
        `grid`,
        $style[`article__banner`],
        { [$style[`article__banner--article`]]: isArticleType },
      ]"
    >
      <Banner
        :m-img-src="bannerInfo.mImgSrc"
        :pc-img-src="bannerInfo.pcImgSrc"
        :target-url="bannerInfo.targetUrl"
        :alt-text="bannerInfo.altText"
        :stretch="false"
        :banner-no="bannerInfo.bannerNo"
        :collect-activity="collectActivity"
      />
    </aside>
    <section v-if="relatedList.length" v-show="!isCardOnMobile">
      <PlainList
        :heading-rank="2"
        :list-type="`related`"
        :list-data="relatedList"
        :update-archive="updateArchivePost"
      />
    </section>
    <section v-show="!isCardOnMobile">
      <PostNav :prev="postInfo.prev || null" :next="postInfo.next || null" />
    </section>
    <Share
      v-if="showShareDialog"
      :heading-rank="2"
      :sns-list="[
        `naverblog`,
        `katalk`,
        `kakaostory`,
        `facebook`,
        `twitter`,
        `linkedin`,
      ]"
      :post-id="postId"
      :share-title="postInfo.title"
      :link-url="cannonicalURL"
      @closed="handleCloseShareDialog"
    />
  </section>
</template>

<script>
import PostHeader from '@organisms/PostHeader';
import PostUtil from '@molecules/PostUtil/index';
import PostHashtags from '@organisms/PostHashtags';
import PostWriter from '@organisms/PostWriter';
import Reply from '@organisms/Reply';
import Banner from '@molecules/Banner/index';
import PlainList from '@organisms/PlainList';
import ReadingBar from '@atoms/ProgressBar';
import PostNav from '@organisms/PostNav';
import Share from '@organisms/Share';
import { mapState, mapGetters } from 'vuex';
import debounce from 'lodash/debounce';

export default {
  components: {
    PostHeader,
    PostUtil,
    PostHashtags,
    PostWriter,
    Reply,
    Banner,
    PlainList,
    ReadingBar,
    PostNav,
    Share,
  },
  props: {
    /**
     * 아티클 데이터 (API 반환 값 사용)
     */
    postInfo: {
      type: Object,
      required: true,
      default: () => ({}),
    },
    /**
     * 포스트 좋아요 설정/해제 처리 API 함수
     */
    updateLikePost: {
      type: Function,
      required: true,
    },
    /**
     * 포스트 담기 설정/해제 처리 API 함수
     */
    updateArchivePost: {
      type: Function,
      required: true,
    },
    /**
     * 댓글 데이터 (@see Reply)
     */
    repliesInfo: {
      type: Object,
      required: true,
      default: () => ({}),
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
     * 좋아요 설정/해제 처리 API 함수
     */
    updateLikeReply: {
      type: Function,
      required: true,
    },
    /**
     * 싫어요 설정/해제 처리 API 함수
     */
    updateDislikeReply: {
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
     * 댓글 수정 API 함수
     */
    updateReply: {
      type: Function,
      required: true,
    },
    /**
     * 유저 정보 API 함수
     */
    getReplyAuthorProfile: {
      type: Function,
      required: true,
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
      required: false,
      default: null,
    },
    /**
     * 신고 API 함수
     */
    submitReport: {
      type: Function,
      required: true,
    },
    /**
     * 아티클 배너 정보
     */
    bannerInfo: {
      type: [Object, null],
      required: false,
      default: null,
    },
    /**
     * 관련 아티클 정보 (related)
     */
    relatedList: {
      type: Array,
      required: false,
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
     * 댓글 열린 상태
     */
    openReply: {
      type: Boolean,
      required: false,
      default: false,
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
  data() {
    return {
      showShareDialog: false,
      readingRate: 0,
    };
  },
  computed: {
    ...mapState(`auth`, [`user`]),
    ...mapState(`common`, [`breakpoint`]),
    ...mapGetters(`common`, [`isPc`]),
    postId() {
      return this.$route.params.id || ``;
    },
    isArticleType() {
      return [`article`, `video`].includes(this.postInfo?.postType);
    },
    isCardType() {
      return [`card`].includes(this.postInfo?.postType);
    },
    ArticleTemplate() {
      return this.isArticleType
        ? () => import(`./${this.postInfo.postType}/index`)
        : null;
    },
    cardTemplate() {
      return this.isCardType
        ? () => import(`@organisms/CardContentViewer`)
        : null;
    },
    ArticleProps() {
      const { postType, cover, contents } = this.postInfo;

      if (postType === `article`) {
        return {
          cover,
          contents,
        };
      } else {
        return {
          videoInfo: contents,
        };
      }
    },
    cannonicalURL() {
      return `${this.$config.PUBLIC_URL}${this.$route.fullPath}`;
    },
    isCardOnMobile() {
      return !this.isPc && this.postInfo?.postType === `card`;
    },
    isFirstCut() {
      const { cut } = this.$route.query;
      return this.isCardType && !(cut && cut * 1);
    },
  },
  mounted() {
    const { collectConnection } = this.$api.collect;
    const { mailSndNo, link } = this.$route?.query;

    this.postInfo?.postType === `article` &&
      document.addEventListener(`scroll`, this.handleScroll, false);

    if (mailSndNo && link) {
      collectConnection({ mailSndNo, link });
    }
  },
  beforeDestroy() {
    this.postInfo?.postType === `article` &&
      document.addEventListener(`scroll`, this.handleScroll, false);
  },
  methods: {
    handleClickShare() {
      this.showShareDialog = true;
    },
    handleCloseShareDialog() {
      this.showShareDialog = false;
    },
    handleChangeSort(payload) {
      /**
       * 댓글 정렬 순서 변경 <br>
       * `{sort, cut}` (selected value)
       * @event change-reply-sort
       */
      this.$emit(`change-reply-sort`, payload);
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
    handleChangedCut(payload) {
      /**
       * 카드뉴스 이미지컷 변경시 발생(답글리스트 갱신)
       *
       * @event changed-cut
       */
      this.$emit(`changed-cut`, payload);
    },
    handleCloseCardViewer() {
      /**
       * 카드뉴스/대화형 컨텐츠 뷰어 나가기
       */
      this.$emit(`close-article-viewer`);
    },
    handleScroll: debounce(function () {
      const container = this.$refs.article.$el;
      const height = container.clientHeight + container.offsetTop;
      const scrollPos = window.scrollY;

      this.readingRate = Math.min((scrollPos / height) * 100, 100);
    }, 300),
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
