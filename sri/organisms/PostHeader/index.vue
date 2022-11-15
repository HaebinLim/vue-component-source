<template>
  <div
    :class="[$style[`post`], { [$style[`post--card`]]: postType === `card` }]"
  >
    <LinkText
      v-if="postType !== `notice`"
      :to="`/contets/${category}`"
      :appearance="`basic`"
      :label="category.toUpperCase()"
      :class="$style[`post__category`]"
    />
    <Heading :rank="1" :text-content="title" :class="$style[`post__title`]" />
    <div :class="$style[`post__info`]">
      <span>
        <span :class="$style[`sr-only`]">최종 수정</span>
        {{ lastModified }}
      </span>
      <span v-if="postType === `article`">
        <span :class="$style[`sr-only`]">리딩 타임</span>
        {{ readTime }}
      </span>
      <PostUtil
        :post-id="postId"
        :context="`icon`"
        :likes.sync="likesCount"
        :archives.sync="archivesCount"
        :liked.sync="pressLike"
        :archived.sync="pressArchive"
        :shares="shares"
        :update-like="updateLike"
        :update-archive="updateArchive"
        :class="$style[`post__util`]"
        @update:likes="updateLikes"
        @update:liked="updateLiked"
        @update:archives="updateArchives"
        @update:archived="updateArchived"
        @click-share="handleClickShare"
      />
    </div>
  </div>
</template>

<script>
import Heading from '@atoms/Heading/index';
import LinkText from '@atoms/Link/Text';
import PostUtil from '@molecules/PostUtil/index';

export default {
  components: {
    Heading,
    LinkText,
    PostUtil,
  },
  props: {
    /**
     * 포스트 ID
     */
    postId: {
      type: [String, Number],
      required: true,
      default: ``,
    },
    /**
     * 아티클 타입
     */
    postType: {
      type: String,
      required: false,
      default: `default`,
      validator(type) {
        return [`default`, `article`, `card`, `video`].includes(type);
      },
    },
    /**
     * 카테고리
     */
    category: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * 타이틀
     */
    title: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * 최종 수정 시간
     */
    lastModified: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * 읽는 데 걸리는 예상 시간
     */
    readTime: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * 좋아요 수
     */
    likes: {
      type: Number,
      required: false,
      default: 0,
    },
    /**
     * 담기 수
     */
    archives: {
      type: Number,
      required: false,
      default: 0,
    },
    /**
     * 공유 수
     */
    shares: {
      type: Number,
      required: false,
      default: 0,
    },
    /**
     * 좋아요 상태
     */
    liked: {
      type: Boolean,
      required: false,
      default: false,
    },
    /**
     * 담기 상태
     */
    archived: {
      type: Boolean,
      required: false,
      default: false,
    },
    /**
     * 좋아요 설정/해제 처리 API 함수
     */
    updateLike: {
      type: Function,
      required: true,
    },
    /**
     * 담기 설정/해제 처리 API 함수
     */
    updateArchive: {
      type: Function,
      required: true,
    },
  },
  data() {
    return {
      likesCount: 0,
      archivesCount: 0,
      pressLike: false,
      pressArchive: false,
    };
  },
  watch: {
    likes(val) {
      this.likesCount = val;
    },
    liked(val) {
      this.pressLike = val;
    },
    archives(val) {
      this.archivesCount = val;
    },
    archived(val) {
      this.pressArchive = val;
    },
  },
  created() {
    const { likes, liked, archives, archived } = this;
    this.likesCount = likes;
    this.archivesCount = archives;
    this.pressLike = liked;
    this.pressArchive = archived;
  },
  methods: {
    updateLikes(val) {
      this.$emit(`update:likes`, val);
    },
    updateLiked(val) {
      this.$emit(`update:liked`, val);
    },
    updateArchives(val) {
      this.$emit(`update:archives`, val);
    },
    updateArchived(val) {
      this.$emit(`update:archived`, val);
    },
    handleClickShare() {
      /**
       * 공유 클릭시 발생 이벤트
       *
       * @event click-share
       */
      this.$emit(`click-share`);
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
