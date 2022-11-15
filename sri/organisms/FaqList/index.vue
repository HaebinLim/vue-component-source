<template>
  <div :class="$style[`faq`]">
    <div :class="$style[`faq__header`]">
      <Heading
        :rank="1"
        :text-content="`고객센터`"
        :class="$style[`faq__header__title`]"
      />
      <p :class="$style[`faq__header__text`]">
        자주 묻는 질문에 대한 답변입니다.<br />
        FEEDBACK을 통해서도 문의 가능합니다.
      </p>
      <LinkText
        :to="`/cs/feedback`"
        :appearance="`basic`"
        :label="`FEEDBACK`"
        :use-under-line="true"
        :has-under-line="true"
        :class="$style[`faq__header__link`]"
      />
    </div>
    <accordion
      :class="$style[`faq__list`]"
      :default-item="current"
      @expanded="setExpandedState"
    >
      <template #accordion>
        <accordion-item
          v-for="({ title, content }, index) in listData"
          :key="index"
          :idx="index"
          :class="[
            $style[`faq__item`],
            { [$style[`faq__item--expanded`]]: index === current },
          ]"
        >
          <template #accordion-header>
            <div :class="$style[`faq__question`]">
              <div :class="$style[`faq__title`]">
                {{ title }}
              </div>
              <div :class="$style[`faq__arrow`]" aria-hidden="true">
                <svgIcon name="head-arrow" :width="7" :height="7" />
              </div>
            </div>
          </template>
          <template #accordion-body>
            <div :class="$style[`faq__answer`]">
              <template v-for="(contObj, idx) in content">
                <Component
                  :is="$articleGenerator.element(contObj)"
                  v-bind="$articleGenerator.attrs(contObj)"
                  :key="idx"
                  :class="[
                    $style['article'],
                    $style[
                      [
                        'article__' +
                          (contObj.type || ``).toLowerCase().replace(/-/g, ''),
                      ]
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
        </accordion-item>
      </template>
    </accordion>
    <Pagination v-bind="paginationOption" />
  </div>
</template>

<script>
import Heading from '@atoms/Heading/index';
import { Accordion, AccordionItem } from '@molecules/Accordion/index';
import LinkText from '@atoms/Link/Text/index';
import Pagination from '@molecules/Pagination';

export default {
  components: {
    Heading,
    Accordion,
    AccordionItem,
    LinkText,
    Pagination,
  },
  props: {
    /**
     * FAQ 리스트 데이터
     */
    listData: {
      type: Array,
      required: true,
      default: () => [],
    },
    /**
     * Pagination 사용시 전달할 옵션
     */
    paginationOption: {
      type: Object,
      required: false,
      default: () => {
        return {};
      },
    },
  },
  data() {
    return {
      current: null,
    };
  },
  methods: {
    setExpandedState(payload) {
      this.current = payload;
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
