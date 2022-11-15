<template>
  <panel
    ref="searchPanel"
    :theme="theme"
    :from="`top`"
    :duration="700"
    :is-modal="true"
    :label="`검색`"
    :label-ref="false"
    :container-class="containerClass"
    :content-class="contentClass"
    :use-header="useHeader"
    @destroyed="handleClosedSearchPanel"
    @ready="handlePanelReady"
  >
    <div :class="[`grid`, $style[`search`]]">
      <div :class="$style[`search__field`]">
        <input
          :id="searchBoxId"
          type="text"
          autocomplete="off"
          spellcheck="false"
          :value="keyword"
          :placeholder="searchBoxPlaceholder"
          :class="[$style[`search__input`]]"
          @input="handleInputSearch"
          @keyup.enter.prevent="handleSearch"
        />
        <BoxButton
          :type="`button`"
          :theme="theme"
          :appearance="`icon`"
          :size="`medium`"
          :label="`검색`"
          :icon="`search`"
          :class="[$style[`search__button`]]"
          @click="handleSearch"
        />
      </div>
    </div>
  </panel>
</template>

<script>
import Panel from '@organisms/Panel';
import BoxButton from '@atoms/Button/Box';

export default {
  components: {
    Panel,
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
      validator(theme) {
        return [`bright`, `dark`].includes(theme);
      },
    },
    /**
     * class name for panel
     */
    containerClass: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * class name for panel__content
     */
    contentClass: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * 패널 오픈 시 Header를 그대로 둘지 여부 (e.g, Search 패널)
     */
    useHeader: {
      type: Boolean,
      required: false,
      default: false,
    },
  },
  data() {
    return {
      keyword: ``,
    };
  },
  computed: {
    searchBoxId() {
      return `search-${this.$_uid}`;
    },
    searchBoxPlaceholder() {
      return `검색어를 입력해주세요`;
    },
  },
  methods: {
    handleSearch() {
      this.handleCloseSearchPanel();
      this.handleClosedSearchPanel();
      this.$router.push(`/search/${this.keyword}`);
    },
    handleCloseSearchPanel() {
      this.$refs.searchPanel?.handleClose();
    },
    handleClosedSearchPanel() {
      /**
       * 패널이 닫힌 후 발생
       * @event closed-search
       */
      this.$emit(`closed-search`);
    },
    handleInputSearch(event) {
      const { value } = event.target;
      this.keyword = value;
    },
    handlePanelReady() {
      /**
       * 패널이 열리면 발생
       * @event opened-search-panel
       */
      this.$emit(`opened-search-panel`);
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
