<template>
  <!-- eslint-disable vue/valid-v-slot -->
  <!-- eslint-disable vue/v-slot-style -->
  <portal v-slot:default to="dialog">
    <transition
      name="dialog"
      appear
      @after-enter="handleTransitionEnter"
      @after-leave="handleTransitionOut"
    >
      <div
        v-if="showDialog"
        ref="dialog"
        tabindex="-1"
        :role="role"
        :aria-label="label"
        :aria-labelledby="labelRef"
        :aria-modal="isModal"
        :class="$style[`dialog`]"
      >
        <div :class="$style[`dialog__content`]">
          <!-- @slot 다이얼로그 컨텐츠 슬롯 -->
          <slot></slot>
        </div>
        <div
          v-if="isModal"
          :class="$style[`dialog__dimmed`]"
          @click="handleClose"
        ></div>
      </div>
    </transition>
  </portal>
</template>

<script>
import { getSiblings, getFocusableElements } from '@/plugins/utils.js';
export default {
  props: {
    role: {
      type: String,
      required: false,
      default: `dialog`,
      validator(role) {
        return [`dialog`, `alertdialog`].includes(role);
      },
    },
    label: {
      type: [String, Boolean],
      required: true,
      default: false,
      validator(label) {
        return ![`팝업`, `popup`, `다이얼로그`, `dialog`, `대화상자`].includes(
          label,
        );
      },
    },
    labelRef: {
      type: [String, Boolean],
      required: true,
      default: false,
    },
    isModal: {
      type: Boolean,
      required: false,
      default: true,
    },
  },
  data() {
    return {
      showDialog: false,
      siblings: [],
      firstFocusableElement: null,
      lastFocusableElement: null,
      scrollTopPosition: 0,
      scrollLeftPosition: 0,
      originFocusedElem: null,
    };
  },
  mounted() {
    this.showDialog = true;
  },
  beforeDestroy() {
    this.unsetInert();
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
      this.showDialog = false;
    },
    storeFocused() {
      this.originFocusedElem = document.activeElement;
    },
    restoreFocus() {
      this.originFocusedElem?.focus();
    },
    setInitalFocus() {
      this.firstFocusableElement.focus();
    },
    setFocusableElems() {
      const focusableElms = getFocusableElements(this.$refs?.dialog);
      this.firstFocusableElement = focusableElms.first || this.$refs?.dialog;
      this.lastFocusableElement = focusableElms.last || this.$refs?.dialog;
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
      this.sliblings = getSiblings(this.$refs?.dialog?.parentElement);
      this.sliblings?.forEach(elm => elm.setAttribute(`aria-hidden`, `true`));
    },
    unsetInert() {
      this.sliblings?.forEach(elm => elm.removeAttribute(`aria-hidden`));
    },
    bindKey(event) {
      const { key, shiftKey, target } = event;
      const keyCode = key.toLowerCase();
      if (keyCode === `escape`) this.handleClose();
      if (keyCode === `tab`) {
        if (target === this.lastFocusableElement && !shiftKey) {
          event.preventDefault();
          this.focusableElms?.focus();
        }
        if (target === this.firstFocusableElement && shiftKey) {
          event.preventDefault();
          this.lastFocusableElement?.focus();
        }
      }
    },
    handleTransitionEnter() {
      this.$nextTick(() => {
        this.storeFocused();
        this.setFocusableElems();
        this.storeScrollPosition();
        this.setInert();
        document.addEventListener(`keydown`, this.bindKey, false);
        this.setInitalFocus();
        /**
         * 컴포넌트가 필요한 모든 처리를 마치고 준비 되었을 때, `ready` 이벤트 발생
         *
         * @event ready
         */
        this.$emit(`ready`);
      });
    },
    handleTransitionOut() {
      this.$destroy();
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
<style lang="scss" src="./transition.scss"></style>
