<template>
  <modal
    ref="dialog"
    :role="`dialog`"
    :label="false"
    :label-ref="`heading-dialag-${$_uid}`"
    :is-modal="true"
    @destroyed="handleDestroy"
  >
    <div :class="$style[`profile`]">
      <heading
        :id="`heading-dialag-${$_uid}`"
        :rank="headingRank"
        :class="$style[`profile__title`]"
      >
        Profile <span :class="$style[`profile__name`]">{{ authorName }}</span>
      </heading>
      <link-text
        :to="`/reply/${authorId}/1`"
        :appearance="`basic`"
        :drop-user-line-space="true"
        :aria-label="`${authorName}의 작성 댓글`"
        :aria-describedby="`description-${$_uid}`"
        :class="$style[`profile__link`]"
      >
        <span :class="$style[`reply__title`]">작성 댓글</span>
        <dl :id="`description-${$_uid}`" :class="$style[`reply__list`]">
          <dt :class="$style[`reply__list__tit`]">총 댓글</dt>
          <dd :class="$style[`reply__list__data`]">{{ totalCount }}</dd>
          <dt :class="$style[`reply__list__tit`]">삭제 댓글</dt>
          <dd :class="$style[`reply__list__data`]">{{ deleteCount }}</dd>
          <dt :class="$style[`reply__list__tit`]">삭제된 댓글</dt>
          <dd :class="$style[`reply__list__data`]">{{ deletedCount }}</dd>
        </dl>
      </link-text>
    </div>
    <BoxButton
      :type="`button`"
      :context="`block`"
      :appearance="`basic`"
      :size="`large`"
      :label="`확인`"
      :class="$style[`profile__btn`]"
      @click="handleClickClose"
    />
  </modal>
</template>

<script>
import { toStringMaxedNumber } from '@/plugins/utils.js';
import Modal from '@organisms/Dialog';
import Heading from '@atoms/Heading/index';
import BoxButton from '@atoms/Button/Box/index';
import LinkText from '@atoms/Link/Text/index';

export default {
  components: {
    Modal,
    Heading,
    BoxButton,
    LinkText,
  },
  props: {
    headingRank: {
      type: Number,
      required: false,
      default: 1,
    },
    authorId: {
      type: [String, Number],
      required: true,
      default: ``,
    },
    authorName: {
      type: String,
      required: true,
      default: ``,
    },
    totalReplies: {
      type: Number,
      required: false,
      default: 0,
    },
    deleteReplies: {
      type: Number,
      required: false,
      default: 0,
    },
    deletedReplies: {
      type: Number,
      required: false,
      default: 0,
    },
  },
  computed: {
    totalCount() {
      return toStringMaxedNumber(this.totalReplies, 9999);
    },
    deleteCount() {
      return toStringMaxedNumber(this.deleteReplies, 9999);
    },
    deletedCount() {
      return toStringMaxedNumber(this.deletedReplies, 9999);
    },
  },
  methods: {
    handleDestroy() {
      this.$emit(`closed`);
    },
    handleClickClose() {
      this.$refs.dialog.handleClose();
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
