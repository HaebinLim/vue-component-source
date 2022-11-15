<template>
  <div :class="[`grid`, $style[`news-list`]]">
    <Heading :rank="1" :class="$style[`news-list__title`]">
      뉴스레터 모아보기
    </Heading>
    <template v-if="years.length > 1">
      <list :tag="`ul`" :class="$style[`news-list__years`]">
        <list-item
          v-for="{ year, isCurrentYear } in years"
          :key="year"
          :tag="`li`"
          :class="$style[`news-list__year`]"
        >
          <link-text
            :to="`/newsletter/${year}`"
            :appearance="isCurrentYear ? `basic` : `grey`"
            :theme="`dark`"
            :class="$style[`news-list__year__link`]"
            :use-under-line="isCurrentYear"
            :has-under-line="isCurrentYear"
            :label="year"
            :aria-current="isCurrentYear ? `page` : false"
          />
        </list-item>
      </list>
    </template>
    <div :class="$style[`news-list__months`]">
      <section
        v-for="(value, name) in listData"
        :key="name"
        :class="$style[`news-list__month`]"
      >
        <Heading :rank="2" :class="$style[`news-list__month__title`]">
          {{ name }}
        </Heading>
        <list :tag="`ul`" :class="$style[`news-list__weeks`]">
          <list-item
            v-for="{ date, displayName } in value"
            :key="date"
            :tag="`li`"
            :class="$style[`news-list__week`]"
          >
            <link-text
              :to="`/newsletter/${currentYear}/${$dayjs(date).format('MMDD')}`"
              :appearance="`basic`"
              :theme="`dark`"
              :drop-user-line-space="true"
              :class="$style[`news-list__week__link`]"
              :label="displayName"
            />
          </list-item>
        </list>
      </section>
    </div>
  </div>
</template>
<script>
import Heading from '@atoms/Heading/index';
import { List, ListItem } from '@molecules/List/index';
import LinkText from '@atoms/Link/Text/index';
export default {
  components: {
    Heading,
    List,
    ListItem,
    LinkText,
  },
  props: {
    /**
     * 현재 연도
     */
    currentYear: {
      type: String,
      required: true,
      default: ``,
    },
    /**
     * 발송한 연도 모음
     */
    years: {
      type: Array,
      required: false,
      default: () => {
        return [];
      },
    },
    /**
     * 월별 메일 목록 Data
     */
    listData: {
      type: Object,
      required: true,
      default: () => {
        return {};
      },
    },
  },
};
</script>
<style lang="scss" module src="./index.scss"></style>
