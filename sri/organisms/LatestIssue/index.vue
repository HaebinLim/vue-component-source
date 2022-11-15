<template>
  <div :class="[`grid`, $style[`latest`]]">
    <Heading
      :rank="2"
      :text-content="`Latest issue`"
      :class="$style[`latest__title`]"
    />
    <list :tag="`ul`" :class="$style[`latest__list`]">
      <list-item
        v-for="{
          articleId,
          title,
          image,
          contentType,
          contentData,
          archived,
          category,
          isSkeleton,
        } in listData"
        :key="articleId"
        :tag="`li`"
        :class="$style[`latest__list__item`]"
      >
        <ItemCard
          :article-id="articleId"
          :title="title"
          :image="image"
          :content-type="contentType"
          :content-data="contentData"
          :archived="archived"
          :category="category"
          :appearance="`bar`"
          :is-skeleton="isSkeleton"
          :use-optional-content="useOptionalContent"
          :update-archive="updateArchive"
          @change-archive="handleChangeArchive"
          @changed-archive="handleChangedArchive"
        />
      </list-item>
    </list>
    <template v-if="showMore">
      <div :class="$style[`latest__more`]">
        <TextButton
          :type="`button`"
          :appearance="`basic`"
          :label="`MORE`"
          :class="$style[`latest__more__btn`]"
          :use-under-line="true"
          @click="handleClickMore"
        />
      </div>
    </template>
  </div>
</template>

<script>
import { List, ListItem } from '@molecules/List/index';
import ItemCard from '@molecules/ItemCard/index';
import Heading from '@atoms/Heading/index';
import TextButton from '@atoms/Button/Text/index';
export default {
  components: { List, ListItem, ItemCard, Heading, TextButton },
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
     * MORE 버튼 노출 여부
     */
    showMore: {
      type: Boolean,
      required: false,
      default: false,
    },
    /**
     * 담기 설정/해제 처리 API 함수
     */
    updateArchive: {
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
        desc: true,
        editorName: false,
        replies: false,
        likes: false,
      };
    },
  },
  methods: {
    handleClickMore() {
      /**
       * MORE버튼 클릭시 발생 이벤트
       *
       * @event click-more
       */
      this.$emit('click-more');
    },
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
