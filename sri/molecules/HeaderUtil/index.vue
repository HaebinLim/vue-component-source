<template>
  <div :class="$style[`util`]">
    <BoxButton
      v-show="!openedPanel"
      ref="searchBtn"
      :type="`button`"
      :context="`inline`"
      :appearance="`icon`"
      :size="`medium`"
      :label="`검색`"
      :theme="theme"
      :icon="`search`"
      :class="$style[`util__item`]"
      aria-haspopup="dialog"
      :aria-expanded="isOpened(`search`)"
      @click="handleSearch"
    />
    <BoxButton
      v-show="!openedPanel"
      ref="profileBtn"
      :type="`button`"
      :context="`inline`"
      :appearance="loginIconType"
      :size="`medium`"
      :label="loginIconLable"
      :theme="theme"
      :icon="`mypage-default`"
      :has-alarm="hasAlarm"
      :class="$style[`util__item`]"
      aria-haspopup="dialog"
      :aria-expanded="isOpened(`profile`)"
      @click="handleProfile"
    />
    <BoxButton
      v-show="!openedPanel"
      ref="menuBtn"
      :type="`button`"
      :context="`inline`"
      :appearance="`icon`"
      :size="`medium`"
      :label="`메뉴`"
      :theme="theme"
      :icon="`menu`"
      :class="$style[`util__item`]"
      aria-haspopup="dialog"
      :aria-expanded="isOpened(`menu`)"
      @click="handleMenu"
    />
    <BoxButton
      v-show="openedPanel"
      :type="`button`"
      :context="`inline`"
      :appearance="`icon`"
      :size="`medium`"
      :label="`${panelName} 닫기`"
      :theme="theme"
      :icon="`close`"
      :class="$style[`util__item`]"
      @click="handleClosePanel"
    />
  </div>
</template>

<script>
import BoxButton from '@atoms/Button/Box';
export default {
  components: {
    BoxButton,
  },
  props: {
    /**
     * 배경 테마
     */
    theme: {
      type: String,
      required: false,
      default: `bright`,
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
     * 메뉴 버튼에 의해 열려진 패널 이름 <br>
     * 이 값이 있어야 닫기 버튼 노출
     */
    openedPanel: {
      type: String,
      required: false,
      default: ``,
      validator(panel) {
        return [``, `search`, `profile`, `menu`].includes(panel);
      },
    },
    /**
     * 닫기 버튼에 대한 레이블 생성 <br>
     * (어떤 패널을 닫는 버튼인지 정보 전달)
     */
    panelName: {
      type: String,
      required: false,
      default: ``,
      vaildator(val) {
        return /닫기|close/.test(val);
      },
    },
    /**
     * 닫기 버튼 클릭 시 콜백 함수 <br>
     * emit 전에 실행
     */
    closePanelHandler: {
      type: [Function, null],
      required: false,
      default: null,
    },
  },
  data() {
    return {
      focusedEl: null,
    };
  },
  computed: {
    loginIconType() {
      return this.isLoggedIn ? `icon` : `icon-text`;
    },
    loginIconLable() {
      return this.isLoggedIn ? `프로필` : `로그인`;
    },
  },
  methods: {
    isOpened(panel) {
      const { openedPanel } = this;
      return panel === openedPanel;
    },
    handleSearch() {
      this.focusedEl = this.$refs.searchBtn;
      /**
       * 검색 버튼 클릭 시 발생 <br>
       * payload로 현재 버튼 전달 (패널을 닫을 때 초점을 되돌려 줄 컴포넌트) <br>
       * `{ focus: Component }`
       *
       * @event click-search
       */
      this.$emit(`click-search`, { focus: this.focusedEl });
    },
    handleProfile() {
      if (this.isLoggedIn) {
        this.focusedEl = this.$refs.profileBtn;
        /**
         * 프로필 버튼 클릭 시 발생 (로그인 상태) <br>
         * payload로 현재 버튼 전달 (패널을 닫을 때 초점을 되돌려 줄 컴포넌트) <br>
         * `{ focus: Component }`
         *
         * @event click-profile
         */
        this.$emit(`click-profile`, { focus: this.focusedEl });
      } else {
        /**
         * 로그인 버튼 클릭 시 발생 (로그인 상태)
         *
         * @event click-login
         */
        this.$emit(`click-login`);
      }
    },
    handleMenu() {
      this.focusedEl = this.$refs.menuBtn;
      /**
       * 메뉴 버튼 클릭 시 발생 <br>
       * payload로 현재 버튼 전달 (패널을 닫을 때 초점을 되돌려 줄 컴포넌트) <br>
       * `{ focus: Component }`
       *
       * @event click-menu
       */
      this.$emit(`click-menu`, { focus: this.focusedEl });
    },
    handleClosePanel() {
      const { closePanelHandler, focusedEl } = this;
      closePanelHandler && closePanelHandler();
      /**
       * 닫기 버튼 클릭 시 발생 <br>
       * `closePanelHandler` 가 전달 되었을 경우 `closePanelHandler`를 실행 후 발생
       *
       * @event click-close
       */
      this.$emit(`click-close`);
      this.$nextTick(() => {
        focusedEl && focusedEl.focus();
      });
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
