<template>
  <header
    :class="[
      `grid`,
      $style[`header`],
      $style[`header--${changeTheme}`],
      { [$style[`header--open-search`]]: showSearchPanel },
      { [$style[`header--open-menu`]]: showMenuPanel },
    ]"
  >
    <Logo :bg-mode="changeTheme" :class="$style[`header__logo`]" />
    <HeaderUtil
      :theme="changeTheme"
      :is-logged-in="isLoggedIn"
      :has-alarm="hasAlarm"
      :panel-name="panelName"
      :opened-panel="openedPanel"
      :class="$style[`header__utils`]"
      @click-search="handleClickSearch"
      @click-profile="handleClickProfile"
      @click-login="handleClickLogin"
      @click-menu="handleClickMenu"
      @click-close="handleClickClose"
    />
    <SearchPanel
      v-if="showSearchPanel"
      ref="search"
      :container-class="$style[`header__search`]"
      :theme="changeTheme"
      :use-header="true"
      @opened-search-panel="preventBodyScroll"
      @closed-search="handleClosedScrollFixPanel"
    />
    <Menu
      v-if="showMenuPanel"
      ref="menu"
      :container-class="$style[`header__menu`]"
      :content-class="$style[`header__menu--full`]"
      @opened-menu-panel="preventBodyScroll"
      @closed-menu="handleClosedScrollFixPanel"
    />
    <MyProfile
      v-if="isLoggedIn && showMyProfile"
      :nick-name="userProfile.nickName"
      :alarms="userProfile.alarms"
      :archives="userProfile.archives"
      :replies="userProfile.replies"
      :show-my-profile="showMyProfile"
      @close-my-profile="handleClosedMyProfile"
      @click-logout="handleClickLogout"
    />
  </header>
</template>

<script>
import Logo from '@atoms/Logo';
import HeaderUtil from '@molecules/HeaderUtil';
import SearchPanel from '@organisms/SearchPanel';
import MyProfile from '@organisms/MyProfile';
import Menu from '@organisms/Menu';

export default {
  components: {
    Logo,
    HeaderUtil,
    SearchPanel,
    Menu,
    MyProfile,
  },
  props: {
    /**
     * 헤더가 적용되는 테마
     */
    theme: {
      type: String,
      required: false,
      default: `bright`,
      validator(theme) {
        return [`bright`, `dark`].includes(theme);
      },
    },
    /**
     * 로그인 상태
     */
    isLoggedIn: {
      type: Boolean,
      required: false,
      default: false,
    },
    /**
     * 새로운 알람 여부
     */
    hasAlarm: {
      type: Boolean,
      required: false,
      default: false,
    },
    /**
     * 메뉴 하단 내부/외부 링크 리스트
     */
    list: {
      type: Array,
      required: false,
      default: () => [],
    },
    /**
     * 로그인 유저 정보 <br>
     * API Response Data 구조 그대로 사용
     */
    userProfile: {
      type: [Object, null],
      required: false,
      default: null,
    },
  },
  data() {
    return {
      storedFocusEl: null,
      showSearchPanel: false,
      showMenuPanel: false,
      showMyProfile: false,
      openedPanel: ``,
      panelName: ``,
    };
  },
  computed: {
    searchBoxId() {
      return `search-${this.$_uid}`;
    },
    searchBoxPlaceholder() {
      return `검색어를 입력해주세요`;
    },
    changeTheme() {
      return this.showMenuPanel ? `dark` : this.theme;
    },
  },
  methods: {
    handleClickSearch({ focus }) {
      this.openedPanel = `search`;
      this.panelName = `검색`;
      this.showSearchPanel = true;
    },
    handleClickProfile() {
      this.showMyProfile = true;
    },
    handleClickLogin() {
      /**
       * 로그인 버튼 클릭 시 발생 (로그인 상태)
       *
       * @event click-login
       */
      this.$emit(`click-login`);
    },
    handleClickMenu({ focus }) {
      this.openedPanel = `menu`;
      this.panelName = `전체메뉴`;
      this.showMenuPanel = true;
    },
    handleClosedScrollFixPanel() {
      this.showSearchPanel = false;
      this.showMenuPanel = false;
      this.openedPanel = ``;
      this.panelName = ``;
      /**
       * 스크롤 차단 해제가 요구될 때 발생
       * @event resume-body-scroll
       */
      this.$emit(`resume-body-scroll`);
    },
    handleClickClose(payload) {
      if (this.openedPanel === `search`) {
        this.$refs.search?.handleCloseSearchPanel();
      } else if (this.openedPanel === `menu`) {
        this.$refs.menu?.handleCloseMenuPanel();
      } else {
        this.$emit(`click-close`, payload);
      }
    },
    preventBodyScroll() {
      /**
       * 스크롤을 차단하는 패널이나 다이얼로그가 열릴 때 발생
       */
      this.$emit(`prevent-body-scroll`);
    },
    handleClosedMyProfile() {
      this.showMyProfile = false;
    },
    handleClickLogout() {
      /**
       * 로그아웃 버튼 클릭시 발생
       * @event click-logout
       */
      this.$emit(`click-logout`);
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
