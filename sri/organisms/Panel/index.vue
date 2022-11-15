<template>
  <!-- eslint-disable vue/valid-v-slot -->
  <!-- eslint-disable vue/v-slot-style -->
  <portal v-slot:default to="panel">
    <div
      ref="panel"
      role="dialog"
      :aria-label="label"
      :aria-labelledby="labelRef"
      :aria-modal="isModal"
      :class="[$style[`panel`], { [containerClass]: containerClass }]"
    >
      <div
        v-if="isModal"
        :class="$style[`panel__dimmed`]"
        @click="handleClose"
      ></div>
      <transition
        appear
        :name="`panel-${from}`"
        @after-enter="handleTransitionEnter"
        @after-leave="handleTransitionOut"
      >
        <div
          v-if="showPanel"
          ref="dialog"
          tabindex="-1"
          :class="[
            $style[`panel__content`],
            $style[`panel__content--${theme}`],
            $style[`panel__content--${from}`],
            { [contentClass]: contentClass },
          ]"
          :style="contentStyleObj"
        >
          <!-- @slot 패널 컨텐츠 슬롯 -->
          <slot></slot>
        </div>
      </transition>
    </div>
  </portal>
</template>

<script>
import { getSiblings, getFocusableElements } from '@/plugins/utils.js';
export default {
  props: {
    /**
     * 배경 테마
     */
    theme: {
      type: String,
      required: false,
      default: `bright`,
      validator(theme) {
        return [``, `bright`, `dark`].includes(theme);
      },
    },
    /**
     * 패널 위치
     */
    from: {
      type: String,
      required: true,
      validator(from) {
        return [`top`, `left`, `bottom`, `right`].includes(from);
      },
    },
    /**
     * 애니메이션 지속 시간 <br>
     * `contentClass`를 통해 CSS로도 부여 가능 <br>
     * `duration`으로 지정 시 `contentClass`보다 우선 순위를 가짐
     */
    duration: {
      type: Number,
      required: false,
      default: 700,
    },
    /**
     * 모달로의 동작 여부 <br>
     * `true`일 경우 dimmed 레이어 깔림
     */
    isModal: {
      type: Boolean,
      required: false,
      default: false,
    },
    /**
     * `aria-label`로 적용 되는 값<br>
     *  *`label` 또는 `labelRef` 중 하나는 반드시 적용 되어야 함*
     */
    label: {
      type: [String, Boolean],
      required: true,
      default: false,
      validator(label) {
        return ![
          `팝업`,
          `popup`,
          `다이얼로그`,
          `dialog`,
          `대화상자`,
          `패널`,
          `panel`,
        ].includes(label);
      },
    },
    /**
     * `aria-labelledby`로 적용 되는 값<br>
     * *`label` 또는 `labelRef` 중 하나는 반드시 적용 되어야 함*
     */
    labelRef: {
      type: [String, Boolean],
      required: true,
      default: false,
    },
    /**
     * class name for panel
     */
    containerClass: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * class name for panel__content
     */
    contentClass: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * 패널 오픈 시 Header를 그대로 둘지 여부 (e.g, Search 패널)
     */
    useHeader: {
      type: Boolean,
      required: false,
      default: false,
    },
  },
  data() {
    return {
      showPanel: false,
      siblings: [],
      firstFocusableElement: null,
      lastFocusableElement: null,
      headerFirstFocusableElement: null,
      headerLastFocusableElement: null,
      scrollTopPosition: 0,
      scrollLeftPosition: 0,
      originFocusedElem: null,
    };
  },
  computed: {
    contentStyleObj() {
      const duration = `${this.duration / 1000}s`;

      return {
        transitionDuration: duration,
      };
    },
  },
  mounted() {
    this.showPanel = true;
  },
  beforeDestroy() {
    this.isModal && this.unsetInert();
    this.restoreScrollPosition();
    document.removeEventListener(`keydown`, this.bindKey, false);
    this.restoreFocus();
  },
  destroyed() {
    /**
     * 컴포넌트가 destroy 되었을 때, `destroyed` 이벤트 발생
     *
     * @event destroyed
     */
    this.$emit(`destroyed`);
  },
  methods: {
    handleClose() {
      this.showPanel = false;
    },
    handleTransitionEnter() {
      this.storeFocused();
      this.setFocusableElems();
      this.storeScrollPosition();
      this.isModal && this.setInert();
      document.addEventListener(`keydown`, this.bindKey, false);
      this.setInitalFocus();
      /**
       * 컴포넌트가 필요한 모든 처리를 마치고 준비 되었을 때, `ready` 이벤트 발생
       *
       * @event ready
       */
      this.$emit(`ready`);
    },
    handleTransitionOut() {
      this.$destroy();
    },
    storeFocused() {
      this.originFocusedElem = document.activeElement;
    },
    restoreFocus() {
      this.originFocusedElem?.focus();
    },
    setInitalFocus() {
      this.firstFocusableElement?.focus();
    },
    setFocusableElems() {
      const { useHeader } = this;
      const focusableElms = getFocusableElements(this.$refs?.dialog);
      const headerFocusableElms = getFocusableElements(
        document.querySelector(`header`),
      );
      this.firstFocusableElement = focusableElms.first || this.$refs?.dialog;
      this.lastFocusableElement = focusableElms.last || this.$refs?.dialog;
      if (useHeader) {
        this.headerFirstFocusableElement = headerFocusableElms.first;
        this.headerLastFocusableElement = headerFocusableElms.last;
      }
    },
    storeScrollPosition() {
      this.scrollTopPosition =
        document.documentElement.scrollTop || document.body.scrollTop;
      this.scrollLeftPosition =
        document.documentElement.scrollLeft || document.body.scrollLeft;
    },
    restoreScrollPosition() {
      window.scrollTo(this.scrollLeftPosition, this.scrollTopPosition);
    },
    setInert() {
      this.siblings = getSiblings(this.$refs?.panel?.parentElement);
      if (this.useHeader) {
        this.siblings = this.siblings.filter(elm => {
          return elm !== document.querySelector(`header`);
        });
      }
      this.siblings?.forEach(elm => elm.setAttribute(`aria-hidden`, `true`));
    },
    unsetInert() {
      this.siblings?.forEach(elm => elm.removeAttribute(`aria-hidden`));
    },
    bindKey(event) {
      const { key, shiftKey, target } = event;
      const {
        useHeader,
        firstFocusableElement,
        lastFocusableElement,
        headerFirstFocusableElement,
        headerLastFocusableElement,
      } = this;
      const keyCode = key.toLowerCase();
      if (keyCode === `escape`) this.handleClose();
      if (keyCode === `tab`) {
        let nextEl;
        if (target === lastFocusableElement && !shiftKey) {
          event.preventDefault();
          nextEl = useHeader
            ? headerFirstFocusableElement
            : firstFocusableElement;
          nextEl = nextEl || headerFirstFocusableElement;
        }
        if (target === firstFocusableElement && shiftKey) {
          event.preventDefault();
          nextEl = useHeader
            ? headerLastFocusableElement
            : lastFocusableElement;
          nextEl = nextEl || headerLastFocusableElement;
        }
        if (target === headerLastFocusableElement && !shiftKey) {
          event.preventDefault();
          nextEl = firstFocusableElement;
        }
        if (target === headerFirstFocusableElement && shiftKey) {
          event.preventDefault();
          nextEl = lastFocusableElement;
        }

        nextEl && nextEl.focus();
      }
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
<style lang="scss" src="./transition.scss"></style>
