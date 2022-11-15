<template>
  <component :is="tag" :role="!isNativeList && `listitem`">
    <!--
      @slot 리스트 아이템 콘텐츠 슬롯
     -->
    <slot>리스트 아이템</slot>
  </component>
</template>

<script>
export default {
  name: `ListItem`,
  props: {
    tag: {
      type: String,
      required: false,
      default: `div`,
      validator(tag) {
        return [`div`, `li`].includes(tag);
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
