<template>
  <modal
    :role="`dialog`"
    :label="false"
    :label-ref="`heading-dialag-${$_uid}`"
    :is-modal="true"
    @destroyed="handleDestroy"
  >
    <div :class="$style[`share`]">
      <Heading
        :id="`heading-dialag-${$_uid}`"
        :rank="headingRank"
        :text-content="`공유하기`"
        :class="$style[`share__heading`]"
      />
      <div :class="$style[`share__copy`]">
        <div :id="`copy-title-${$_uid}`" :class="$style[`share__copy__header`]">
          <b>링크 주소</b>
          <text-button
            :type="`button`"
            :theme="`bright`"
            :appearance="`basic`"
            :label="`복사`"
            :use-under-line="false"
            :aria-label="`복사: 링크 주소`"
            :class="$style[`share__copy__button`]"
            @click="handleCopy"
          />
        </div>
        <p :class="$style[`share__url`]">{{ copyingUrl }}</p>
      </div>
      <div :class="$style[`share__sns`]">
        <template v-for="{ icon, label } in shareSns">
          <IconButton
            :key="icon"
            :type="`button`"
            :context="`inline`"
            :appearance="`circle`"
            :size="`large`"
            :label="label"
            :icon="icon"
            @click="handleShare(icon, postId)"
          />
        </template>
      </div>
    </div>
  </modal>
</template>

<script>
import Modal from '@organisms/Dialog';
import Heading from '@atoms/Heading';
import TextButton from '@atoms/Button/Text';
import IconButton from '@atoms/Button/Box';
import { snsShare } from '@/plugins/utils';
import { mapState } from 'vuex';

const SNS_LIST = [
  {
    label: `네이버 블로그`,
    icon: `naverblog`,
  },
  {
    label: `카카오톡`,
    icon: `katalk`,
  },
  {
    label: `카카오 스토리`,
    icon: `kakaostory`,
  },
  {
    label: `페이스북`,
    icon: `facebook`,
  },
  {
    label: `트위터`,
    icon: `twitter`,
  },
  {
    label: `링크드인`,
    icon: `linkedin`,
  },
];

export default {
  components: {
    Modal,
    Heading,
    TextButton,
    IconButton,
  },
  props: {
    /**
     * 대화상자 제목 레벨
     */
    headingRank: {
      type: Number,
      required: true,
    },
    /**
     * 카피 될 URL
     */
    linkUrl: {
      type: String,
      required: true,
      default: ``,
    },
    /**
     * list of using sns
     */
    snsList: {
      type: Array,
      required: true,
      default: () => [],
      validator(list) {
        const predicate = name => !!SNS_LIST.find(sns => sns.icon === name);
        return list.every(predicate);
      },
    },
    /**
     * SNS로 전달할 제목
     */
    shareTitle: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * SNS로 전달할 해시태그 (twitter)
     */
    hashTags: {
      type: Array,
      required: false,
      default: () => [],
    },
    /**
     * 공유할 포스트 ID
     */
    postId: {
      type: [Number, String],
      required: true,
      default: 0,
    },
  },
  head() {
    return {
      script: [
        {
          hid: `kakao-sdk`,
          src: `//developers.kakao.com/sdk/js/kakao.min.js`,
          callback: () => {
            if (!process.browser) return;
            const Kakao = window.Kakao;
            if (!Kakao.isInitailized) Kakao.init(this.$config.KAKAO_KEY);
          },
        },
      ],
    };
  },
  computed: {
    ...mapState(`common`, [`breakpoint`]),
    copyingUrl() {
      return this.linkUrl || this.$route.path;
    },
    shareSns() {
      const { snsList } = this;
      return SNS_LIST.filter(sns => snsList.includes(sns.icon));
    },
  },
  beforeDestroy() {
    if (!process.browser) return;
    const Kakao = window.Kakao;
    Kakao.cleanup();
  },
  methods: {
    handleCopy() {
      navigator.clipboard
        .writeText(this.copyingUrl)
        .then(() => {
          alert(`링크가 복사됐습니다.`);
        })
        .catch(() => {
          alert(this.$msg(`share.error.can_not_copy`));
        });
    },
    handleShare(sns, id) {
      const { collectActivity } = this.$api.collect;
      snsShare(sns)(this.copyingUrl, this.shareTitle, this.hashTags);
      collectActivity({
        collectType: `SHARE_CONTENTS`,
        deviceType: this.breakpoint === `pc` ? `PC` : `M`,
        value: id,
      });
    },
    handleDestroy() {
      /**
       * 팝업이 닫힐 때 발생
       *
       * @event closed
       */
      this.$emit(`closed`);
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
