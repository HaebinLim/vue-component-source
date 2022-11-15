<template>
  <div :class="$style[`footer`]">
    <PostUtil
      :post-id="postId"
      :context="`viewer-mobile`"
      :replies="replies"
      :likes="likesCount"
      :archives="archivesCount"
      :shares="shares"
      :liked="pressLike"
      :archived="pressArchive"
      @click-reply="handleClickReply"
      @click-like="handleClickLike"
      @click-archive="handleClickArchive"
      @click-share="handleClickShare"
    />
  </div>
</template>

<script>
import PostUtil from '@molecules/PostUtil/index';

export default {
  components: {
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
     * 댓글 수
     */
    replies: {
      type: Number,
      required: false,
      default: 0,
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
  created() {
    const { likes, liked, archives, archived } = this;
    this.likesCount = likes;
    this.archivesCount = archives;
    this.pressLike = liked;
    this.pressArchive = archived;
  },
  methods: {
    handleClickReply() {
      /**
       * 댓글 클릭시 발생 이벤트
       *
       * @event click-reply
       */
      this.$emit(`click-reply`);
    },
    handleClickLike() {
      this.updateLike(!this.pressLike)
        .then(({ likes, liked }) => {
          this.likesCount = likes;
          this.pressLike = liked;
        })
        .catch(error => {
          const { message } = error;
          alert(this.$msg(message));
        });
    },
    handleClickArchive() {
      this.updateArchive(!this.pressArchive)
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
       */
      this.$emit(`click-share`);
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
