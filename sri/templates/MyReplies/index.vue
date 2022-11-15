<template>
  <div :class="[`grid`, $style[`replies`]]">
    <div :class="$style[`replies__header`]">
      <Heading
        :rank="1"
        :class="$style[`replies__header__title`]"
        :text-content="repliesPageTitle"
      />
      <dl :class="$style[`replies__header__count`]">
        <dt :class="$style[`tit`]">총 댓글</dt>
        <dd :class="$style[`data`]">
          {{ repliesTotalCount }}
        </dd>
        <dt :class="$style[`tit`]">삭제 댓글</dt>
        <dd :class="$style[`data`]">
          {{ repliesDeleteCount }}
        </dd>
        <dt :class="$style[`tit`]">삭제된 댓글</dt>
        <dd :class="$style[`data`]">
          {{ repliesDeletedByCount }}
        </dd>
      </dl>
    </div>
    <div :class="$style[`replies__wrap`]">
      <SelectBox
        :label="`정렬 방식`"
        :name="`sort`"
        :value="sortValue"
        :options="sortOptions"
        :class="$style[`replies__sort`]"
        @change="handleChangeSort"
      />
      <table :class="$style[`replies__table`]">
        <caption>
          작성 댓글 리스트
        </caption>
        <thead>
          <tr>
            <th scope="col" :class="$style[`replies__table__col`]">콘텐츠</th>
            <th scope="col" :class="$style[`replies__table__col`]">댓글</th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="{
              articleId,
              category,
              title,
              content,
              replyId,
              mentionName,
              lastModified,
              likes,
              dislikes,
            } in listData"
            :key="replyId"
          >
            <td>
              <link-text
                :to="`/post/${articleId}`"
                :appearance="`basic`"
                :class="$style[`article`]"
              >
                <span :class="$style[`article__category`]">
                  {{ category.toUpperCase() }}
                </span>
                <span :class="$style[`article__title`]">{{ title }}</span>
              </link-text>
            </td>
            <td>
              <button
                type="button"
                :class="$style[`reply`]"
                @click="handleClickReply(articleId, replyId)"
              >
                <span v-if="mentionName" :class="$style[`reply__mention`]">
                  {{ repliesMention(mentionName) }}
                </span>
                <span :class="$style[`reply__content`]">{{ content }}</span>
                <span :class="$style[`reply__info`]">
                  <span :class="$style[`reply__modified`]">
                    {{ $dayjs(lastModified).format(`YYYY.MM.DD hh:mm`) }}
                  </span>
                  <span :class="$style[`reply__count`]">
                    <span :class="$style[`reply__count__num`]">
                      <svgIcon
                        name="recommend-stroke"
                        :width="16"
                        :height="16"
                        :class="$style[`icon`]"
                      />
                      {{ repliesLikesCount(likes) }}
                    </span>
                    <span :class="$style[`reply__count__num`]">
                      <svgIcon
                        name="unrecommend-stroke"
                        :width="16"
                        :height="16"
                        :class="$style[`icon`]"
                      />
                      {{ repliesDislikesCount(dislikes) }}
                    </span>
                  </span>
                </span>
              </button>
            </td>
          </tr>
        </tbody>
      </table>
      <Pagination v-bind="paginationOption" />
    </div>
  </div>
</template>
<script>
import Heading from '@atoms/Heading/index';
import SelectBox from '@atoms/SelectBox/index';
import LinkText from '@atoms/Link/Text/index';
import Pagination from '@molecules/Pagination/index';
import { toStringMaxedNumber } from '~/plugins/utils';

export default {
  components: {
    Heading,
    SelectBox,
    LinkText,
    Pagination,
  },
  props: {
    /**
     * 사용자 아이디
     */
    userId: {
      type: [String, Number],
      required: true,
      default: ``,
    },
    /**
     * 댓글 작성자 아이디
     */
    authorId: {
      type: [String, Number],
      required: true,
      default: ``,
    },
    /**
     * 댓글 작성자명
     */
    authorName: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * 총 댓글
     */
    totalCount: {
      type: Number,
      required: false,
      default: 0,
    },
    /**
     * 삭제 댓글
     */
    deleteCount: {
      type: Number,
      required: false,
      default: 0,
    },
    /**
     * 삭제된 댓글
     */
    deletedByCount: {
      type: Number,
      required: false,
      default: 0,
    },
    /**
     * 작성 댓글 리스트 데이터
     */
    listData: {
      type: Array,
      required: true,
      default: () => {
        return [];
      },
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
  },
  data() {
    return {
      sortValue: `contRegDtm,desc`,
      sortOptions: [
        {
          text: `최신순`,
          value: `contRegDtm,desc`,
        },
        {
          text: `추천수`,
          value: `recommendCnt,desc`,
        },
        {
          text: `순추천수`,
          value: `popularity,desc`,
        },
      ],
    };
  },
  computed: {
    repliesTotalCount() {
      return toStringMaxedNumber(this.totalCount, 9999);
    },
    repliesDeleteCount() {
      return toStringMaxedNumber(this.deleteCount, 9999);
    },
    repliesDeletedByCount() {
      return toStringMaxedNumber(this.deletedByCount, 9999);
    },
    repliesLikesCount() {
      return likes => {
        return toStringMaxedNumber(likes, 9999);
      };
    },
    repliesDislikesCount() {
      return dislikes => {
        return toStringMaxedNumber(dislikes, 9999);
      };
    },
    repliesMention() {
      return mentionName => {
        return `@` + mentionName;
      };
    },
    repliesPageTitle() {
      return this.userId === this.authorId
        ? `작성 댓글`
        : `${this.authorName}의 작성 댓글`;
    },
  },
  methods: {
    handleChangeSort(val) {
      this.sortValue = val;
      /**
       * 정렬옵션 변경시 발생 이벤트
       *
       * @event change-sorting
       */
      this.$emit('change-sorting', val);
    },
    handleClickReply(articleId, replyId) {
      this.$api.comments
        .getCommentPosition({
          id: replyId,
          size: 10,
          sort: `grpRegDtm,desc`,
        })
        .then(({ page }) => {
          this.$router.push({
            path: `/post/${articleId}`,
            query: { repPage: page },
            hash: `reply`,
          });
        })
        .catch(error => {
          const msg = error?.detail;
          alert(msg || this.$msg(`default`));
        });
    },
  },
};
</script>
<style lang="scss" module src="./index.scss"></style>
