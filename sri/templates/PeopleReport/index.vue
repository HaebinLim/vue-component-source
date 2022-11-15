<template>
  <div :class="[`grid`, $style[`people-report`]]">
    <Heading
      :rank="1"
      :text-content="`People Report`"
      :class="$style[`people-report__title`]"
    />
    <PeopleReportItem :report="report" @click-download="handleClickDownload" />
  </div>
</template>
<script>
import Heading from '@atoms/Heading/index';
import PeopleReportItem from '@molecules/PeopleReportItem/index';

export default {
  components: {
    Heading,
    PeopleReportItem,
  },
  props: {
    /**
     * people report 리스트
     */
    report: {
      type: Array,
      required: true,
      default: () => [],
    },
  },
  methods: {
    handleClickDownload(payload) {
      this.$emit(`click-download`, payload);
    },
  },
};
</script>
<style lang="scss" module src="./index.scss"></style>
