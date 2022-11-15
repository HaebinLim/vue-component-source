<template>
  <div :class="[`grid`, $style[`category-best`]]">
    <heading :rank="2" :class="$style[`category-best__title`]">
      Best {{ listCategory }} Top 5
    </heading>
    <div :class="$style[`category-best__controls`]">
      <BoxButton
        :type="`button`"
        :appearance="`icon`"
        :size="`small`"
        :label="autoplaying ? `자동 넘김 일시 정지` : `자동 넘김 시작`"
        :icon="autoplaying ? `best-pause` : `best-play`"
        :class="$style[`category-best__controls__autoplay`]"
        @click="toggleAutoPlaying"
      />
      <BoxButton
        :type="`button`"
        :appearance="`arrow-left`"
        :size="`large`"
        :label="`이전 슬라이드`"
        :class="$style[`category-best__controls__prev`]"
        @click="prevSlide"
      />
      <BoxButton
        :type="`button`"
        :appearance="`arrow-right`"
        :size="`large`"
        :label="`다음 슬라이드`"
        :class="$style[`category-best__controls__next`]"
        @click="nextSlide"
      />
    </div>
    <div
      :class="$style[`category-best__item`]"
      @mousemove="handleMouseEnter"
      @mouseleave="handleMouseLeave"
    >
      <flicking
        ref="carousel"
        :options="carouselOptions"
        :plugins="plugins"
        :class="$style[`category-best__images`]"
        @changed="handleChange"
        @before-resize="handleBeforeResize"
        @after-resize="handleAfterResize"
        @keydown.left.native="handleLeftArrowKey"
        @keydown.right.native="handleRightArrowKey"
        @focusin.native="handleMouseEnter"
        @focusout.native="handleFocusOut"
        @move-start="handleMoveStart"
        @move-end="handleMoveEnd"
      >
        <template
          v-for="({ articleId, image, archived }, index) in bestTopList"
        >
          <div :key="articleId" :class="$style[`category-best__image-wrap`]">
            <img
              :src="$resolveImageUrl(image)"
              :tabindex="index === currIdx ? null : `-1`"
              alt=""
              @click="handleArticleLink"
            />
            <span :class="$style[`category-best__image-idx`]">
              0{{ index + 1 }}
            </span>
            <BoxButton
              :appearance="`icon`"
              :tabindex="index === currIdx ? null : `-1`"
              :label="archived ? `담은 콘텐츠에서 제거` : `담은 콘텐츠에 추가`"
              :type="`button`"
              :hide-label="true"
              :size="`small`"
              :icon="archived ? `contents-remove` : `contents-add`"
              :class="$style[`category-best__archive`]"
              @click="handleArchiveClick(articleId)"
            />
          </div>
        </template>
      </flicking>
      <div :class="$style[`category-best__info-wrap`]">
        <link-text
          :appearance="`basic`"
          :to="articleUrl"
          :class="$style[`category-best__title-link`]"
          @focusin.native="handleMouseEnter"
          @focusout.native="handleFocusOut"
        >
          <span :class="$style[`category-best__title`]">
            {{ title }}
          </span>
          <span :class="$style[`category-best__desc`]">
            {{ desc }}
          </span>
        </link-text>
        <LinkText
          :label="editorName"
          :appearance="`basic`"
          :to="editorUrl"
          :class="$style[`category-best__editor`]"
          :aria-label="`${editorName}님의 글 모아보기`"
          @focusin.native="handleMouseEnter"
          @focusout.native="handleFocusOut"
        />
        <div
          :class="$style[`category-best__meta`]"
          tabindex="-1"
          @click="handleArticleLink"
        >
          <span>댓글</span>
          <span>{{ repliesCount }}</span>
          <span>좋아요</span>
          <span>{{ likesCount }}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { Flicking } from '@egjs/vue-flicking';
import { AutoPlay } from '@egjs/flicking-plugins';
import LinkText from '@atoms/Link/Text/index';
import BoxButton from '@atoms/Button/Box/index';
import Heading from '@atoms/Heading/index';
import debounce from 'lodash/debounce';
import { getFocusableElements, toStringMaxedNumber } from '@/plugins/utils.js';
import Easing from 'easing-functions';
require('@egjs/vue-flicking/dist/flicking.css');
const getDescFromArticle = contentData => {
  contentData = contentData ? JSON.parse(contentData) : [];
  return (
    contentData.find(el => {
      return el.type === 'body';
    })?.value ?? null
  );
};
export default {
  components: {
    LinkText,
    BoxButton,
    Heading,
    Flicking,
  },
  props: {
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
     * 담기 설정/해제 함수
     */
    updateArchive: {
      type: Function,
      required: true,
    },
    /**
     * 현재 카테고리
     */
    listCategory: {
      type: String,
      required: true,
    },
  },
  data() {
    return {
      animationState: false,
      currIdx: 0,
      bestTopList: [],
      autoplaying: true,
      delay: 3000,
      carouselOptions: {
        circular: true,
        resizeOnContentsReady: true,
        autoResize: true,
        duration: 500,
        align: `prev`,
        easing: Easing.Quadratic.In,
        moveType: `snap`,
        noPanelStyeOverride: true,
      },
      keyPressed: false,
    };
  },
  computed: {
    currArticle() {
      return this.bestTopList[this.currIdx];
    },
    title() {
      return this.currArticle.title;
    },
    desc() {
      const { contentType, contentData } = this.currArticle;
      switch (contentType) {
        case 'article':
          return getDescFromArticle(contentData?.content);
        case 'video':
          return contentData?.videoExln;
        case 'card':
          return contentData[0]?.cardExln;
        default:
          return null;
      }
    },
    editorName() {
      return this.currArticle.editorName;
    },
    likesCount() {
      return toStringMaxedNumber(this.currArticle.likes, 9999);
    },
    repliesCount() {
      return toStringMaxedNumber(this.currArticle.replies, 9999);
    },
    articleUrl() {
      return `/post/${this.currArticle.articleId}`;
    },
    editorUrl() {
      return `/editor/${this.currArticle.editorId}`;
    },
    plugins() {
      return [this.autoplay];
    },
  },
  watch: {
    autoplaying(val) {
      if (this.autoplay) {
        val ? this.autoplay.play() : this.autoplay.stop();
      }
    },
  },
  created() {
    this.bestTopList = [...this.listData];
    this.autoplay = new AutoPlay({
      duration: this.delay,
      direction: 'NEXT',
      stopOnHover: false,
    });
  },
  methods: {
    handleArticleLink() {
      this.$router.push(this.articleUrl);
    },
    handleArchiveClick(articleId) {
      const { bestTopList } = this;
      const entry = bestTopList.find(item => item.articleId === articleId);
      const idx = bestTopList.findIndex(item => item.articleId === articleId);
      const state = !bestTopList[idx].archived;
      if (this.updateArchive) {
        this.updateArchive({ id: articleId, state: !state })
          .then(({ archived }) => {
            bestTopList.splice(idx, 1, { ...entry, archived });
            /**
             * 콘텐츠 담기 상태 변경 시 발생 <br>
             * `true/false` : 변경 성공 여부<br>
             * @event changed-archive
             */
            this.$emit('changed-archive', true);
          })
          .catch(error => {
            /**
             * 콘텐츠 담기 상태 변경 시 발생
             * `true/false` : 변경 성공 여부<br>
             * @event changed-archive
             */
            this.$emit('changed-archive', false);
            const msg = error?.detail || this.$msg(`archive.error.not_changed`);
            alert(msg);
          });
      } else {
        /**
         * `updateArchive`를 전달하지 않았을 경우, <br>
         * 담기 설정/해제 버튼 클릭 시 발생 <br>
         * `{id, state}`
         * @event change-archive
         */
        this.$emit(`change-archive`, {
          id: articleId,
          state: !state,
        });
      }
    },
    toggleAutoPlaying() {
      this.autoplaying = !this.autoplaying;
    },
    handleMouseEnter: debounce(function () {
      this.autoplay.stop();
    }, 150),
    handleMouseLeave: debounce(function () {
      this.autoplaying && this.autoplay.play();
    }, 150),
    prevSlide() {
      if (this.animationState) return;
      this.$refs.carousel.prev();
    },
    nextSlide() {
      if (this.animationState) return;
      this.$refs.carousel.next();
    },
    handleChange(event) {
      const { index, panel } = event;
      this.currIdx = index;
      if (this.keyPressed) {
        getFocusableElements(panel.element)?.first?.focus();
      }
    },
    handleLeftArrowKey() {
      this.keyPressed = true;
      this.prevSlide();
    },
    handleRightArrowKey() {
      this.keyPressed = true;
      this.nextSlide();
    },
    handleFocusOut() {
      this.keyPressed = false;
      this.autoplaying && this.autoplay.play();
    },
    handleBeforeResize(e) {
      this.autoplay.stop();
    },
    handleAfterResize(e) {
      e.currentTarget.init();
      this.autoplaying && this.autoplay.play();
    },
    handleMoveStart(e) {
      this.animationState = true;
    },
    handleMoveEnd(e) {
      this.animationState = false;
    },
  },
};
</script>
<style lang="scss" module src="./index.scss"></style>
