<template>
  <div
    :class="[
      $style[`plain`],
      { [$style[`plain--related`]]: listType === `related` },
    ]"
  >
    <div :class="[`grid`]">
      <template v-if="listType === `editor` && editorMeta">
        <div
          :class="[$style[`plain__header`], $style[`plain__header--editor`]]"
        >
          <ProfileImage
            v-if="editorMeta.image"
            :type="`editor`"
            :image-url="editorMeta.image"
            :class="$style[`plain__header__profile`]"
          />
          <div :class="$style[`plain__header__wrap`]">
            <heading
              :rank="headingRank"
              :class="$style[`plain__header__title`]"
            >
              {{ titleStr
              }}<template v-if="totalCount">
                <span :class="$style[`plain__header__title__count`]">{{
                  resultCount
                }}</span>
              </template>
            </heading>
            <p :class="$style[`plain__header__copy`]">{{ editorMeta.intro }}</p>
          </div>
        </div>
      </template>
      <template v-else>
        <div :class="$style[`plain__header`]">
          <heading :rank="headingRank" :class="$style[`plain__header__title`]">
            {{ titleStr
            }}<template v-if="totalCount">
              <span :class="$style[`plain__header__title__count`]">{{
                resultCount
              }}</span>
            </template>
          </heading>
        </div>
      </template>
    </div>
    <div
      :class="[
        $style[`plain__wrap`],
        {
          [$style[`plain__wrap--bg`]]:
            listType !== `archived` && listType !== `related`,
        },
      ]"
    >
      <div :class="`grid`">
        <div
          v-if="listType !== `related`"
          :class="[
            $style[`plain__utility`],
            { [$style[`plain__utility--archived`]]: listType === `archived` },
          ]"
        >
          <CheckBox
            v-if="listType === `archived`"
            :label="`전체 선택`"
            :value="`checked`"
            :checked="selectAllChecked"
            @change="handleSelectAll"
          />
          <TextButton
            v-if="listType === `archived`"
            :type="`button`"
            :appearance="`basic`"
            :label="`선택 삭제`"
            :class="$style[`plain__utility__delete`]"
            :disabled="deleteBtnDisabled"
            @click="handleDeleteSelected"
          />
          <SelectBox
            :label="`정렬 방식`"
            :name="`sort`"
            :value="sort"
            :options="sortOptions"
            :class="$style[`plain__utility__sort`]"
            @change="handleChangeSort"
          />
        </div>
        <div :class="$style[`plain__list`]">
          <list :tag="`ul`" :class="$style[`plain__list__wrap`]">
            <list-item
              v-for="(
                {
                  articleId,
                  title,
                  image,
                  category,
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
              :class="$style[`plain__list__item`]"
              :style="{ order: index > 5 ? index + 1 : index }"
            >
              <ItemCard
                :article-id="articleId"
                :title="title"
                :image="image"
                :category="category"
                :checked="
                  listType === `archived`
                    ? selectedArticles.includes(articleId)
                    : null
                "
                :archived="archived"
                :content-type="contentType"
                :content-data="contentData"
                :replies="replies"
                :likes="likes"
                :editor-name="editorName"
                :editor-id="editorId"
                :appearance="listType === `related` ? `simple` : `basic`"
                :is-skeleton="isSkeleton"
                :use-optional-content="useOptionalContent"
                :update-archive="updateArchive"
                @change-selected="handleChangeSelected"
                @changed-archive="handleChangedArchive"
                @change-archive="handleChangeArchive"
              />
            </list-item>
          </list>
          <Banner
            v-if="listType !== `related` && bannerData"
            v-bind="bannerData"
            :class="$style[`plain__list__banner`]"
            :style="{ order: 6 }"
          />
        </div>
        <template
          v-if="listType !== `archived` && listType !== `related` && showMore"
        >
          <div :class="$style[`plain__more`]">
            <TextButton
              :type="`button`"
              :appearance="`basic`"
              :label="`MORE`"
              :class="$style[`plain__more__btn`]"
              @click="handleClickMore"
            />
          </div>
        </template>
        <Pagination
          v-if="listType === `archived`"
          v-bind="paginationOption"
          :class="$style[`plain__pagination`]"
        />
      </div>
    </div>
  </div>
</template>

<script>
import Heading from '@atoms/Heading/index';
import TextButton from '@atoms/Button/Text/index';
import SelectBox from '@atoms/SelectBox/index';
import { List, ListItem } from '@molecules/List/index';
import ItemCard from '@molecules/ItemCard/index';
import Banner from '@molecules/Banner/index';
import Pagination from '@molecules/Pagination';
import ProfileImage from '@atoms/ProfileImage';
import CheckBox from '@atoms/CheckBox';
import { toStringMaxedNumber } from '@/plugins/utils.js';
export default {
  components: {
    List,
    ListItem,
    ItemCard,
    Heading,
    TextButton,
    Banner,
    CheckBox,
    SelectBox,
    ProfileImage,
    Pagination,
  },
  props: {
    /**
     * 목록 종류
     */
    listType: {
      type: String,
      required: false,
      default: `related`,
      validator(ctx) {
        return [`archived`, `search`, `tag`, `editor`, `related`].includes(ctx);
      },
    },
    /**
     *섹션 헤딩 레벨
     */
    headingRank: {
      type: Number,
      required: true,
      default: 2,
    },
    /**
     * 정렬 기본값
     */
    sort: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * 목록 조회 기준
     */
    query: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * 작성글 리스트일경우 작성자 정보
     */
    editorMeta: {
      type: Object,
      required: false,
      default: () => {
        return {};
      },
    },
    /**
     * API에서 받아온 배너 데이터
     */
    bannerData: {
      type: Object,
      required: false,
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
     * 전체 글 갯수
     */
    totalCount: {
      type: Number,
      required: false,
      default: 0,
    },
    /**
     * 정렬 옵션
     */
    sortOptions: {
      type: Array,
      required: false,
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
     * Pagination 사용시 전달할 옵션
     */
    paginationOption: {
      type: Object,
      required: false,
      default: () => {
        return {};
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
  data() {
    return {
      selectAllChecked: false,
      selectedArticles: [],
    };
  },
  computed: {
    useOptionalContent() {
      return {
        category: true,
        checked: this.listType === `archived`,
        desc: this.listType !== `related`,
        editorName: this.listType !== `related`,
        replies: this.listType !== `related`,
        likes: this.listType !== `related`,
      };
    },
    deleteBtnDisabled() {
      return !(this.selectedArticles.length > 0);
    },
    titleStr() {
      switch (this.listType) {
        case `archived`:
          return `담은콘텐츠`;
        case `related`:
          return `Related`;
        case `editor`:
          return `${this.editorMeta.name}의 작성글`;
        default:
          return this.query;
      }
    },
    resultCount() {
      return toStringMaxedNumber(this.totalCount, 99999999);
    },
  },
  methods: {
    handleChangeSort(val) {
      /**
       * 정렬옵션 변경시 발생 이벤트 <br>
       * `value`
       *
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
    handleSelectAll({ checked }) {
      this.selectAllChecked = checked;
      this.selectedArticles = [];
      for (let i = 0, len = this.listData.length; i < len; i++) {
        if (checked) {
          this.selectedArticles.push(this.listData[i].articleId);
        }
      }
    },
    handleDeleteSelected() {
      /**
       * 선택 삭제 버튼 클릭시 발생 이벤트 <br>
       * `[id]`
       * @event change-selected
       */
      this.$emit('delete-selected', this.selectedArticles);
    },
    handleChangeSelected({ checked, value }) {
      const n = this.selectedArticles.indexOf(value);
      if (checked) {
        if (n < 0) {
          this.selectedArticles.push(value);
        }
      } else if (n > -1) {
        this.selectedArticles.splice(n, 1);
      }
      this.selectAllChecked = false;
    },
  },
};
</script>
<style lang="scss" module src="./index.scss"></style>
