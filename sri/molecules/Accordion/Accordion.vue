<template>
  <div
    :class="[
      $style[`accordion`],
      { [$style[`accordion--expanded`]]: expanded },
    ]"
  >
    <div
      :id="accHeaderId"
      role="button"
      tabindex="0"
      :aria-expanded="expanded"
      :aria-controls="accPanelId"
      :class="$style[`accordion__header`]"
      @click="handleEvent"
      @keyup.enter="handleEvent"
      @keyup.space="handleEvent"
    >
      <!-- @slot 아코디언 아이템별 헤더 영역에 들어갈 내용 -->
      <slot name="accordion-header"></slot>
    </div>
    <div
      :id="accPanelId"
      role="region"
      :aria-labelledby="accHeaderId"
      :class="$style[`accordion__body`]"
    >
      <!-- @slot 아코디언 아이템별 본문 영역에 들어갈 내용 -->
      <slot name="accordion-body"></slot>
    </div>
  </div>
</template>
<script>
export default {
  inject: ['status'],
  props: {
    idx: { type: Number, required: true },
  },
  data() {
    return {
      accPanelId: `acc-panel-${this.$_uid}`,
      accHeaderId: `acc-header-${this.$_uid}`,
    };
  },
  computed: {
    expanded() {
      return this.status.current === this.idx;
    },
  },
  methods: {
    handleEvent() {
      this.status.current = this.expanded ? null : this.idx;
    },
  },
};
</script>
<style lang="scss" module src="./AccordionItem.scss"></style>
