<template>
  <flicking
    ref="carousel"
    :options="carouselOptions"
    :plugins="plugins"
    :class="$style[`best`]"
    @changed="handleChange"
    @keydown.left.native="handleLeftArrowKey"
    @keydown.right.native="handleRightArrowKey"
    @focusin.native="handleFocusIn"
    @focusout.native="handleFocusOut"
    @mousemove.native="handleMouseEnter"
    @mouseleave.native="handleMouseLeave"
  >
    <template v-for="({ id, title, image, archived }, idx) in bestTopList">
      <div :key="id" :class="[$style[`best-item`]]">
        <nuxt-link
          :to="`/post/${id}`"
          :aria-label="title"
          :tabindex="idx === currIdx ? null : `-1`"
          :aria-hidden="idx !== currIdx"
          :class="$style[`best-item__header`]"
        >
          <span :class="$style[`best-item__header__number`]">
            {{ (idx + 1).toString().padStart(2, `0`) }}
          </span>
          <p :class="$style[`best-item__header__title`]">{{ title }}</p>
        </nuxt-link>
        <div :class="$style[`best-item__image__container`]">
          <img
            :src="$resolveImageUrl(image)"
            alt=""
            :class="$style[`best-item__image`]"
          />
          <BoxButton
            :appearance="`icon`"
            :label="
              archived
                ? `콘텐츠 담기 해제 - ${title}`
                : `콘텐츠 담기 - ${title}`
            "
            :type="`button`"
            :hide-label="true"
            :size="`medium`"
            :icon="archived ? `contents-remove` : `contents-add`"
            :tabindex="idx === currIdx ? null : `-1`"
            :class="$style[`best-item__archive`]"
            @click="handleArchive(id, archived)"
          />
        </div>
      </div>
    </template>
  </flicking>
</template>

<script>
import { Flicking } from '@egjs/vue-flicking';
import { AutoPlay, Arrow } from '@egjs/flicking-plugins';
import BoxButton from '@atoms/Button/Box/index';
import { getFocusableElements } from '@/plugins/utils.js';
import Easing from 'easing-functions';
import debounce from 'lodash/debounce';
require('@egjs/vue-flicking/dist/flicking.css');
const mobileAlign = { camera: `15px`, panel: `0%` };
const tabletAlign = { camera: `24px`, panel: `0%` };

export default {
  components: {
    Flicking,
    BoxButton,
  },
  props: {
    /**
     * best top 5 컨텐츠 목록
     */
    list: {
      type: Array,
      required: true,
      default: () => [],
    },
    /**
     * 담기 설정/해제 함수
     */
    updateArchive: {
      type: Function,
      required: true,
    },
    /**
     * 현재 해상도 정보
     */
    breakpoint: {
      type: String,
      required: true,
      default: `mobile`,
      validator(breakpoint) {
        return [`mobile`, `tablet`].includes(breakpoint);
      },
    },
    /**
     * 자동 넘김 제어 상태
     */
    autoplaying: {
      type: Boolean,
      required: true,
      default: false,
    },
    /**
     * 자동 넘김 지연 시간 (ms)
     */
    dealay: {
      type: Number,
      required: false,
      default: 3000,
    },
    /**
     * 애니메이션 동작 시간 (ms)
     */
    duration: {
      type: Number,
      required: false,
      default: 500,
    },
  },
  data() {
    return {
      currIdx: 0,
      bestTopList: [],
      autoplay: null,
      carouselDefaults: {
        circular: true,
        resizeOnContentsReady: true,
        autoResize: true,
        panelsPerView: 1.4,
        duration: this.duration,
        easing: Easing.Quadratic.In,
      },
      keyPressed: false,
    };
  },
  computed: {
    plugins() {
      return [this.autoplay];
    },
    carouselOptions() {
      return this.breakpoint === `mobile`
        ? { ...this.carouselDefaults, panelsPerView: 1.4, align: mobileAlign }
        : { ...this.carouselDefaults, panelsPerView: 1.6, align: tabletAlign };
    },
  },
  watch: {
    breakpoint(val) {
      /**
       * breakpoint가 변경 될 때는 슬라이드 초기화
       * 간혹 offset이 어긋나는 경우가 있어서 resize 호출
       */
      this.$refs.carousel.align = val === `mobile` ? mobileAlign : tabletAlign;
      this.$refs.carousel.panelsPerView = val === `mobile` ? 1.4 : 1.6;
      this.$refs.carousel.destroy();
      this.$refs.carousel.init();
      this.$refs.carousel.resize();
    },
    autoplaying(val) {
      if (this.autoplay) {
        val ? this.autoplay.play() : this.autoplay.stop();
      }
    },
  },
  created() {
    this.bestTopList = [...this.list];
    this.autoplay = new AutoPlay({
      duration: this.dealay,
      direction: 'NEXT',
      stopOnHover: false,
    });
  },
  mounted() {
    this.$nextTick(() => {
      this.$refs.carousel.addPlugins(new Arrow({ parentEl: this.$parent.$el }));
    });
  },
  methods: {
    handleArchive(id, state) {
      const { bestTopList } = this;
      this.updateArchive({
        id,
        state: !state,
      })
        .then(({ archived }) => {
          const entry = bestTopList.find(item => item.id === id);
          const idx = bestTopList.findIndex(item => item.id === id);
          bestTopList.splice(idx, 1, { ...entry, archived });
        })
        .catch(error => {
          const msg = error?.detail || this.$msg(`archive.error.not_changed`);
          alert(msg);
        });
    },
    handleMouseEnter: debounce(function () {
      this.autoplay.stop();
    }, 150),
    handleMouseLeave: debounce(function () {
      this.autoplaying && this.autoplay.play();
    }, 150),
    prevSlide() {},
    nextSlide() {},
    handleChange(event) {
      const { index, panel } = event;
      this.currIdx = index;
      if (this.keyPressed) {
        getFocusableElements(panel.element)?.first?.focus();
        this.keyPressed = false;
      }
    },
    handleLeftArrowKey() {
      this.keyPressed = true;
      this.$refs.carousel.prev();
    },
    handleRightArrowKey() {
      this.keyPressed = true;
      this.$refs.carousel.next();
    },
    handleFocusIn() {
      this.autoplaying && this.autoplay.stop();
      this.keyPressed = true;
    },
    handleFocusOut(event) {
      if (!this.keyPressed && !this.$el.contains(document.activeElement)) {
        this.keyPressed = false;
        this.autoplaying && this.autoplay.play();
      }
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
