<template>
  <panel
    ref="menuPanel"
    :from="`top`"
    :is-modal="false"
    :label="`전체메뉴`"
    :label-ref="false"
    :duration="700"
    :theme="`dark`"
    :use-header="true"
    :container-class="containerClass"
    :content-class="contentClass"
    @destroyed="handleClosedMenuPanel"
    @ready="handlePanelReady"
  >
    <nav :class="[`grid`, $style[`menu`]]">
      <MainNavigation :class="$style[`menu__list`]" />
      <SubNavigation :links="links" :class="$style[`menu__list`]" />
    </nav>
  </panel>
</template>

<script>
import Panel from '@organisms/Panel/index';
import MainNavigation from '@molecules/MainNavigation/index';
import SubNavigation from '@molecules/SubNavigation/index';

export default {
  components: {
    Panel,
    MainNavigation,
    SubNavigation,
  },
  props: {
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
     * 메뉴 하단 내부/외부 링크
     */
    links: {
      type: Array,
      required: false,
      default: () => [],
    },
  },
  watch: {
    $route(to, from) {
      this.handleCloseMenuPanel();
    },
  },
  methods: {
    handleCloseMenuPanel() {
      this.$refs.menuPanel?.handleClose();
    },
    handleClosedMenuPanel() {
      /**
       * 패널이 닫힌 후 발생
       * @event closed-menu
       */
      this.$emit(`closed-menu`);
    },
    handlePanelReady() {
      /**
       * 패널이 열리면 발생
       * @event opened-menu-panel
       */
      this.$emit(`opened-menu-panel`);
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
