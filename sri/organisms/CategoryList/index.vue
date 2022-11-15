<template>
  <div :class="[`grid`, $style[`category`]]">
    <div :class="$style[`category__header`]">
      <heading :rank="2" :class="$style[`category__header__title`]">
        {{ listCategory
        }}<template v-if="totalCount">
          <span :class="$style[`category__header__title__count`]">{{
            categoryCount
          }}</span>
        </template>
      </heading>
      <SelectBox
        :label="`정렬방식`"
        :name="`sort`"
        :value="sort"
        :options="sortOptions"
        @change="handleChangeSort"
      />
    </div>
    <div :class="$style[`category__list`]">
      <list :tag="`ul`" :class="$style[`category__list__wrap`]">
        <list-item
          v-for="(
            {
              articleId,
              title,
              image,
              archived,
              contentType,
              contentData,
              replies,
              likes,
              editorName,
              editorId,
              isSkeleton,
            },
            index
          ) in listData"
          :key="articleId"
          :tag="`li`"
          :class="[
            $style[`category__list__item`],
            {
              [$style['category__list__item--large']]:
                index % 8 === Math.floor(index / 8) % 2,
            },
          ]"
          :style="{ order: index > 7 ? index + 1 : index }"
        >
          <ItemCard
            :article-id="articleId"
            :title="title"
            :image="image"
            :archived="archived"
            :archived-btn-size="
              index % 8 === Math.floor(index / 8) % 2 ? `medium` : `small`
            "
            :content-type="contentType"
            :content-data="contentData"
            :replies="replies"
            :likes="likes"
            :editor-name="editorName"
            :editor-id="editorId"
            :list-category="listCategory"
            :is-emphasis="index === 0"
            :is-skeleton="isSkeleton"
            :use-optional-content="useOptionalContent"
            :update-archive="updateArchive"
            @change-archive="handleChangeArchive"
            @changed-archive="handleChangedArchive"
          />
        </list-item>
      </list>
      <Banner
        v-if="bannerData"
        v-bind="bannerData"
        :class="$style[`category__list__banner`]"
        :style="{ order: 8 }"
        :stretched="true"
      />
    </div>
    <template v-if="showMore">
      <div :class="$style[`category__more`]">
        <TextButton
          :type="`button`"
          :appearance="`basic`"
          :label="`MORE`"
          :class="$style[`category__more__btn`]"
          @click="handleClickMore"
        />
      </div>
    </template>
  </div>
</template>

<script>
import Heading from '@atoms/Heading/index';
import TextButton from '@atoms/Button/Text/index';
import SelectBox from '@atoms/SelectBox/index';
import { List, ListItem } from '@molecules/List/index';
import ItemCard from '@molecules/ItemCard/index';
import Banner from '@molecules/Banner/index';
import { toStringMaxedNumber } from '@/plugins/utils.js';
export default {
  components: {
    List,
    ListItem,
    ItemCard,
    Heading,
    TextButton,
    Banner,
    SelectBox,
  },
  props: {
    /**
     * API에서 받아온 배너 데이터
     */
    bannerData: {
      type: [Object, null],
      required: true,
      default: () => {
        return {};
      },
    },
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
     * 전체 글 갯수
     */
    totalCount: {
      type: Number,
      required: false,
      default: 0,
    },
    /**
     * 현재 카테고리
     */
    listCategory: {
      type: String,
      required: true,
    },
    /**
     * 현재 사용하고 있는 정렬값
     */
    sort: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * 정렬 옵션
     */
    sortOptions: {
      type: Array,
      required: true,
      default: () => {
        return [];
      },
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
        category: false,
        checked: false,
        desc: true,
        editorName: true,
        replies: true,
        likes: true,
      };
    },
    categoryCount() {
      return toStringMaxedNumber(this.totalCount, 99999999);
    },
  },
  methods: {
    handleChangeSort(val) {
      /**
       * 정렬옵션 변경시 발생 이벤트 <br>
       * `value`
       * @event change-sorting
       */
      this.$emit('change-sorting', val);
    },
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
