<template>
  <panel
    ref="myProfilePanel"
    :from="`right`"
    :is-modal="true"
    :label="`label`"
    :label-ref="false"
    :content-class="$style[`my-profile__panel`]"
    @destroyed="handlePanelDestroyed"
  >
    <div :class="$style[`my-profile`]">
      <section :class="$style[`my-profile__info`]">
        <heading
          :id="`heading-dialag-${$_uid}`"
          :rank="1"
          :class="$style[`my-profile__title`]"
        >
          Profile
          <span :class="$style[`my-profile__name`]">{{ nickName }}</span>
        </heading>
        <link-text
          :to="`/member/notification`"
          :appearance="`basic`"
          :drop-user-line-space="true"
          :class="$style[`my-profile__link`]"
          :aria-label="`받은 알림 ${alarmsCount}건`"
        >
          <dl :class="$style[`my-profile__link__inner`]">
            <dt :class="$style[`my-profile__link__subject`]">
              <span
                v-if="alarms.hasNew"
                :class="$style[`my-profile__link__subject--new`]"
              >
                새
              </span>
              알림
            </dt>
            <dd
              :class="[
                $style[`my-profile__link__value`],
                { [$style[`my-profile__link__value--new`]]: alarms.hasNew },
              ]"
            >
              {{ alarmsCount }}
            </dd>
          </dl>
        </link-text>
        <link-text
          :to="`/member/archive/1`"
          :appearance="`basic`"
          :drop-user-line-space="true"
          :class="$style[`my-profile__link`]"
          :aria-label="`담은 콘텐츠 ${archivesCount}건`"
        >
          <dl :class="$style[`my-profile__link__inner`]">
            <dt :class="$style[`my-profile__link__subject`]">담은 콘텐츠</dt>
            <dd :class="$style[`my-profile__link__value`]">
              {{ archivesCount }}
            </dd>
          </dl>
        </link-text>
        <link-text
          :to="`/member/reply/1`"
          :appearance="`basic`"
          :drop-user-line-space="true"
          :class="$style[`my-profile__link`]"
        >
          <span :class="$style[`my-profile__link__subject`]">작성 댓글</span>
          <dl :id="`replies-${$_uid}`" :class="$style[`reply__list`]">
            <dt :class="$style[`reply__list__tit`]">총 댓글</dt>
            <dd :class="$style[`reply__list__data`]">
              {{ repliesTotalCount }}
            </dd>
            <dt :class="$style[`reply__list__tit`]">삭제 댓글</dt>
            <dd :class="$style[`reply__list__data`]">
              {{ repliesDeleteCount }}
            </dd>
            <dt :class="$style[`reply__list__tit`]">삭제된 댓글</dt>
            <dd :class="$style[`reply__list__data`]">
              {{ repliesDeletedByCount }}
            </dd>
          </dl>
        </link-text>
      </section>
      <div :class="$style[`my-profile__utils`]">
        <link-text
          :to="`/member/profile/`"
          :appearance="`basic`"
          :use-under-line="true"
          :has-under-line="true"
          :label="`프로필 수정`"
          :class="$style[`my-profile__utils__link`]"
        />
        <link-text
          :href="`https://www.thepllab.com/mypage`"
          :target="`_blank`"
          :appearance="`basic`"
          :use-under-line="true"
          :has-under-line="true"
          :label="`통합 계정 수정`"
          :class="$style[`my-profile__utils__link`]"
        />
        <TextButton
          :appearance="`basic`"
          :type="`button`"
          :use-under-line="true"
          :has-under-line="true"
          :label="`로그아웃`"
          :class="$style[`my-profile__utils__logout`]"
          @click="handleLogoutClick"
        />
      </div>
      <BoxButton
        :appearance="`icon`"
        :type="`button`"
        :size="`medium`"
        :icon="`close`"
        :label="`닫기`"
        :hide-label="true"
        :class="$style[`my-profile__close`]"
        @click="handleClosePanel"
      />
    </div>
  </panel>
</template>
<script>
import Heading from '@atoms/Heading/index';
import LinkText from '@atoms/Link/Text/index';
import BoxButton from '@atoms/Button/Box/index';
import TextButton from '@atoms/Button/Text/index';
import Panel from '@organisms/Panel/index';
import { toStringMaxedNumber } from '@/plugins/utils.js';
export default {
  components: {
    Panel,
    Heading,
    LinkText,
    BoxButton,
    TextButton,
  },
  props: {
    /**
     * (현재)사용자 이름
     */
    nickName: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * (현재)사용자 알림 관련 데이터 count:Number, hasNew:Boolean
     */
    alarms: {
      type: Object,
      required: false,
      default: () => {
        return {
          count: 0,
          hasNew: false,
        };
      },
    },
    /**
     * (현재)사용자 담은 글 관련 데이터 count:Number
     */
    archives: {
      type: Object,
      required: false,
      default: () => {
        return {
          count: 0,
        };
      },
    },
    /**
     * (현재)사용자 답글 관련 데이터 totalCount:Number, deleteCount:Number, deletedByCount:Number
     */
    replies: {
      type: Object,
      required: false,
      default: () => {
        return {
          totalCount: 0,
          deleteCount: 0,
          deletedByCount: 0,
        };
      },
    },
  },
  computed: {
    alarmsCount() {
      return toStringMaxedNumber(this.alarms.count, 9999);
    },
    archivesCount() {
      return toStringMaxedNumber(this.archives.count, 9999);
    },
    repliesTotalCount() {
      return toStringMaxedNumber(this.replies.totalCount, 9999);
    },
    repliesDeleteCount() {
      return toStringMaxedNumber(this.replies.deleteCount, 9999);
    },
    repliesDeletedByCount() {
      return toStringMaxedNumber(this.replies.deletedByCount, 9999);
    },
  },
  watch: {
    $route(to, from) {
      this.handleClosePanel();
    },
  },
  methods: {
    handleLogoutClick() {
      /**
       * 로그아웃 버튼 클릭시 발생
       * @event click-logout
       */
      this.$emit(`click-logout`);
    },
    handleClosePanel() {
      this.$refs.myProfilePanel?.handleClose();
    },
    handlePanelDestroyed() {
      /**
       * 패널 닫힐 때 발생
       * @event close-my-profile
       */
      this.$emit('close-my-profile');
    },
  },
};
</script>
<style lang="scss" module src="./index.scss"></style>
