<template>
  <div :class="$style[`home`]">
    <MainList
      :list-data="curationData"
      :update-archive="updateArchive"
      :collect-activity="collectActivity"
      @change-archive="handleChangeArchive"
      @changed-archive="handleChangedArchive"
    />
    <div v-if="bannerData1" :class="[`grid`, $style[`home__banner1`]]">
      <Banner
        v-bind="bannerData1"
        :stretched="isBannerStretched"
        :collect-activity="collectActivity"
      />
    </div>
    <MainBestTop
      v-if="bestData.length"
      :breakpoint="breakpoint"
      :heading-rank="2"
      :list="bestData"
      :update-archive="updateArchive"
      :class="$style[`home__best`]"
    />
    <div v-if="bannerData2" :class="[`grid`, $style[`home__banner2`]]">
      <Banner
        v-bind="bannerData2"
        :stretched="isBannerStretched"
        :collect-activity="collectActivity"
      />
    </div>
    <LatestIssue
      :list-data="listData"
      :update-archive="updateArchive"
      :show-more="showMore"
      @change-archive="handleChangeArchive"
      @changed-archive="handleChangedArchive"
      @click-more="handleClickMore"
    />
    <WebinaBanner v-if="webinaBannerData" v-bind="webinaBannerData" />
    <div v-if="bannerData3" :class="[`grid`, $style[`home__banner3`]]">
      <Banner
        v-bind="bannerData3"
        :stretched="isBannerStretched"
        :collect-activity="collectActivity"
      />
    </div>
    <VoteBanner v-if="voteBannerData" v-bind="voteBannerData" />
    <RecruitBanner v-if="recruitBannerData" v-bind="recruitBannerData" />
  </div>
</template>

<script>
import MainBestTop from '@organisms/MainBestTop';
import MainList from '@organisms/MainList';
import LatestIssue from '@organisms/LatestIssue';
import Banner from '@molecules/Banner';
import WebinaBanner from '@organisms/WebinaBanner';
import VoteBanner from '@organisms/VoteBanner';
import RecruitBanner from '@organisms/RecruitBanner';
import { mapState } from 'vuex';
export default {
  components: {
    MainBestTop,
    MainList,
    LatestIssue,
    Banner,
    WebinaBanner,
    VoteBanner,
    RecruitBanner,
  },
  props: {
    /**
     * ??????1 ?????????
     */
    bannerData1: {
      type: [Object, null],
      required: false,
      default: null,
    },
    /**
     * ??????2 ?????????
     */
    bannerData2: {
      type: [Object, null],
      required: false,
      default: null,
    },
    /**
     * ??????3 ?????????
     */
    bannerData3: {
      type: [Object, null],
      required: false,
      default: null,
    },
    /**
     * ?????????????????? ?????????
     */
    curationData: {
      type: Array,
      required: true,
      default: () => {
        return [];
      },
    },
    /**
     * ????????? ?????????
     */
    listData: {
      type: Array,
      required: true,
      default: () => {
        return [];
      },
    },
    /**
     * ??????????????? ?????????
     */
    bestData: {
      type: Array,
      required: false,
      default: () => {
        return [];
      },
    },
    /**
     * ??????????????? ?????????
     */
    webinaBannerData: {
      type: [Object, null],
      required: false,
      default: null,
    },
    /**
     * ???????????? ?????????
     */
    voteBannerData: {
      type: [Object, null],
      required: false,
      default: null,
    },
    /**
     * ???????????? ?????????
     */
    recruitBannerData: {
      type: [Object, null],
      required: false,
      default: null,
    },
    /**
     * ???????????? ??????
     */
    recruitBannerTitle: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * ???????????? ???????????????
     */
    recruitBannerLinkText: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * MORE ?????? ?????? ?????? (LatestIssue)
     */
    showMore: {
      type: Boolean,
      required: false,
      default: false,
    },
    /**
     * ?????? ??????/?????? ?????? API ??????
     */
    updateArchive: {
      type: Function,
      required: false,
      default: undefined,
    },
    /**
     * ???????????? ?????? ?????? API ??????
     */
    collectActivity: {
      type: Function,
      required: false,
      default: undefined,
    },
  },
  computed: {
    isBannerStretched() {
      return this.breakpoint === `mobile`;
    },
    ...mapState(`common`, [`breakpoint`]),
  },
  methods: {
    handleClickMore() {
      /**
       * MORE?????? ????????? ?????? ?????????
       *
       * @event click-more
       */
      this.$emit('click-more');
    },
    handleChangedArchive(payload) {
      /**
       * ????????? ?????? ?????? ?????? ??? ??????
       * @event changed-archive
       */
      this.$emit('changed-archive', payload);
    },
    handleChangeArchive(paylaod) {
      /**
       * ????????? ?????? ?????? ?????? ??? ?????? <br>
       * `{id, state}`
       * @event change-archive
       */
      this.$emit('change-archive', paylaod);
    },
  },
};
</script>
<style lang="scss" module src="./index.scss"></style>
