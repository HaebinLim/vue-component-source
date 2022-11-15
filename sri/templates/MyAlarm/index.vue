<template>
  <div :class="[`grid`, $style[`alarm`]]">
    <heading :rank="2" :class="$style[`alarm__header`]">
      알림<span v-if="totalCount" :class="$style[`alarm__header__count`]">{{
        alarmCount
      }}</span>
    </heading>
    <div :class="$style[`alarm__wrap`]">
      <table :class="$style[`alarm__table`]">
        <caption>
          알림 리스트
        </caption>
        <thead>
          <tr>
            <th scope="col" :class="$style[`alarm__table__col`]">내용</th>
            <th scope="col" :class="$style[`alarm__table__col`]">일시</th>
            <th scope="col" :class="$style[`alarm__table__col`]">
              <TextButton
                :appearance="`basic`"
                :label="`전체 삭제`"
                :class="$style[`alarm__delete`]"
                @click="handleClickDeleteAll"
              />
            </th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="{ code, id, articleId, title, lastModified } in listData"
            :key="id"
          >
            <th scope="row">
              <link-text
                :to="`/post/${articleId}`"
                :appearance="`basic`"
                :class="$style[`alarm__title`]"
              >
                <template v-if="code === `NT001`">{{ title }}</template>
                <template v-else>
                  게시글에 남긴 코멘트가 추천받았습니다.
                </template>
              </link-text>
            </th>
            <td>
              <span :class="$style[`alarm__date`]">{{ lastModified }}</span>
            </td>
            <td>
              <TextButton
                :appearance="`basic`"
                :label="`삭제`"
                :class="$style[`alarm__delete`]"
                @click="handleClickDelete(id)"
              />
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
import TextButton from '@atoms/Button/Text';
import Pagination from '@molecules/Pagination/index';
import LinkText from '@atoms/Link/Text/index';
import { toStringMaxedNumber } from '~/plugins/utils';

export default {
  components: {
    Heading,
    TextButton,
    Pagination,
    LinkText,
  },
  props: {
    /**
     * 전체 알림 갯수
     */
    totalCount: {
      type: Number,
      required: false,
      default: 0,
    },
    /**
     * 알림 리스트 데이터
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
  computed: {
    alarmCount() {
      return toStringMaxedNumber(this.totalCount, 99);
    },
  },
  methods: {
    handleClickDeleteAll() {
      /**
       * 전체 삭제 버튼 클릭시 발생
       * @event click-delete-all
       */
      this.$emit(`click-delete-all`);
    },
    handleClickDelete(id) {
      /**
       * 삭제 버튼 클릭시 발생
       * @event click-delete
       */
      this.$emit(`click-delete`, {
        id,
      });
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
