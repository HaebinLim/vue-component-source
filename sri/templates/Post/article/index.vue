<template>
  <div :class="[`grid`, $style[`article__container`]]">
    <div :class="[`grid--full`, $style[`article__cover`]]">
      <img :src="$resolveImageUrl(cover)" alt="" />
    </div>
    <template v-for="(contObj, idx) in contents">
      <Component
        :is="$articleGenerator.element(contObj)"
        :key="idx"
        v-bind="$articleGenerator.attrs(contObj)"
        :class="[
          $style['article'],
          $style[
            ['article__' + (contObj.type || ``).toLowerCase().replace(/-/g, '')]
          ],
          {
            grid:
              (contObj.type || ``).toLowerCase().replace(/-/g, '') ===
              `slideimg`,
          },
          {
            'grid--full':
              (contObj.type || ``).toLowerCase().replace(/-/g, '') ===
              `slideimg`,
          },
        ]"
        v-html="$articleGenerator.html(contObj, $style)"
      />
    </template>
  </div>
</template>

<script>
import Flicking from '@egjs/flicking';
import { Arrow } from '@egjs/flicking-plugins';
import Easing from 'easing-functions';
require('@egjs/flicking/dist/flicking.css');

export default {
  props: {
    /**
     * 커버 이미지
     */
    cover: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * 포스트 콘텐츠
     */
    contents: {
      type: Array,
      required: true,
      default: () => [],
    },
  },
  data() {
    return {
      carousels: [],
    };
  },
  mounted() {
    this.initSlides();
  },
  beforeDestroy() {
    this.destroySlides();
  },
  methods: {
    initSlides() {
      this.carousels = [...document.querySelectorAll(`.flicking-viewport`)].map(
        entry => {
          const controllerParentEl = entry.parentElement.querySelector(
            `.${this.$style.article__slideimg__controls}`,
          );

          const flicking = new Flicking(entry, {
            circular: false,
            panelsPerView: 1.07,
            align: `prev`,
            easing: Easing.Quadratic.In,
          });

          flicking.addPlugins(
            new Arrow({
              parentEl: controllerParentEl,
            }),
          );

          return flicking;
        },
      );
    },
    destroySlides() {
      this.carousels.forEach(flicking => {
        flicking.destroy();
      });
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
