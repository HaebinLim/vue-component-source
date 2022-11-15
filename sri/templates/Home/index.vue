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
     * 배너1 데이터
     */
    bannerData1: {
      type: [Object, null],
      required: false,
      default: null,
    },
    /**
     * 배너2 데이터
     */
    bannerData2: {
      type: [Object, null],
      required: false,
      default: null,
    },
    /**
     * 배너3 데이터
     */
    bannerData3: {
      type: [Object, null],
      required: false,
      default: null,
    },
    /**
     * 메인큐레이션 데이터
     */
    curationData: {
      type: Array,
      required: true,
      default: () => {
        return [];
      },
    },
    /**
     * 최신글 데이터
     */
    listData: {
      type: Array,
      required: true,
      default: () => {
        return [];
      },
    },
    /**
     * 메인베스트 데이터
     */
    bestData: {
      type: Array,
      required: false,
      default: () => {
        return [];
      },
    },
    /**
     * 웨비나배너 데이터
     */
    webinaBannerData: {
      type: [Object, null],
      required: false,
      default: null,
    },
    /**
     * 투표배너 데이터
     */
    voteBannerData: {
      type: [Object, null],
      required: false,
      default: null,
    },
    /**
     * 채용배너 데이터
     */
    recruitBannerData: {
      type: [Object, null],
      required: false,
      default: null,
    },
    /**
     * 채용배너 제목
     */
    recruitBannerTitle: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * 채용배너 링크텍스트
     */
    recruitBannerLinkText: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * MORE 버튼 노출 여부 (LatestIssue)
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
    isBannerStretched() {
      return this.breakpoint === `mobile`;
    },
    ...mapState(`common`, [`breakpoint`]),
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
