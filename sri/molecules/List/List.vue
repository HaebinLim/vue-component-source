<template>
  <component :is="tag" :role="!isNativeList && `list`">
    <!--
      @slot 리스트 콘텐츠 슬롯
     -->
    <slot>리스트 컨텐츠</slot>
  </component>
</template>

<script>
export default {
  name: `List`,
  props: {
    tag: {
      type: String,
      required: false,
      default: `div`,
      validator(tag) {
        return [`div`, `ol`, `ul`].includes(tag);
      },
    },
  },
  computed: {
    isNativeList() {
      return this.tag === `li`;
    },
  },
};
</script>
