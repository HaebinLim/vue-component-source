<template>
  <div :class="[`grid`, $style[`main`]]">
    <list :tag="`ul`" :class="$style[`main__list`]">
      <list-item
        v-for="(
          { articleId, title, image, archived, category, isSkeleton, uiPotnCd },
          index
        ) in listData"
        :key="index"
        :tag="`li`"
        :class="[
          $style[`main__list__item`],
          { [$style['main__list__item--large']]: index === 0 },
        ]"
      >
        <ItemCard
          :ui-potn-cd="uiPotnCd"
          :article-id="articleId"
          :title="title"
          :image="image"
          :archived="archived"
          :category="category"
          :is-emphasis="index === 0"
          :appearance="`simple`"
          :is-skeleton="isSkeleton"
          :archived-btn-size="index === 0 ? `medium` : `small`"
          :use-optional-content="useOptionalContent"
          :collect-activity="collectActivity"
          :update-archive="updateArchive"
          @change-archive="handleChangeArchive"
          @changed-archive="handleChangedArchive"
        />
      </list-item>
    </list>
  </div>
</template>

<script>
import { List, ListItem } from '@molecules/List/index';
import ItemCard from '@molecules/ItemCard/index';
export default {
  components: { List, ListItem, ItemCard },
  props: {
    /**
     * API 에서 가져온 리스트 데이터
     */
    listData: {
      type: Array,
      required: true,
    },
    /**
     * 담기 설정/해제 처리 API 함수
     */
    updateArchive: {
      type: Function,
      required: false,
      default: undefined,
    },
    /**
     * 활동내역 수집 처리 API 함수
     */
    collectActivity: {
      type: Function,
      required: false,
      default: undefined,
    },
  },
  computed: {
    useOptionalContent() {
      return {
        category: true,
        checked: false,
        desc: false,
        editorName: false,
        replies: false,
        likes: false,
      };
    },
  },
  methods: {
    handleChangedArchive(payload) {
      /**
       * 콘텐츠 담기 상태 변경 시 발생
       * @event changed-archive
       */
      this.$emit('changed-archive', payload);
    },
    handleChangeArchive(paylaod) {
      /**
       * 콘텐츠 담기 버튼 클릭 시 발생 <br>
       * `{id, state}`
       * @event change-archive
       */
      this.$emit('change-archive', paylaod);
    },
  },
};
</script>
<style lang="scss" module src="./index.scss"></style>
