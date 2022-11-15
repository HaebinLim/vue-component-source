<template>
  <div :class="$style[`combobox`]" @keydown.esc="handleEsc">
    <label
      :for="id"
      :class="[
        $style[`combobox__label`],
        { [$style[`combobox__label--hide`]]: !showLabel },
      ]"
    >
      {{ label }}
    </label>
    <div :class="[$style[`combobox__field`]]">
      <input
        :id="id"
        type="text"
        role="combobox"
        autocomplete="off"
        spellcheck="false"
        :value="value"
        :placeholder="placeholder"
        :aria-controls="`${id}-list`"
        :aria-haspopup="!!optionList.length"
        :aria-expanded="visibleOptionList.toString()"
        :aria-activedescendant="activatedDescendant"
        :class="[$style[`combobox__input`]]"
        @input="handleInput"
        @keydown.enter="handleEnter"
        @keydown.down.prevent="handleArrowDown"
        @keydown.up.prevent="handleArrowUp"
      />
      <BoxButton
        :type="`button`"
        :appearance="`icon`"
        :size="`medium`"
        :label="`검색`"
        :icon="`search`"
        :class="[$style[`combobox__button`]]"
        @click="handleSubmit"
      />
    </div>
    <div
      v-if="visibleOptionList"
      :id="`${id}-list`"
      role="listbox"
      aria-label="검색 결과"
    >
      <div
        v-for="(option, idx) in optionList"
        :id="`${id}-option-${idx}`"
        :key="`${id}-option-${idx}`"
        tabindex="-1"
        role="option"
        :aria-selected="idx === selectedItem"
        :class="{
          [selectedClassName]: selectedClassName && idx === selectedItem,
        }"
        @click="handleClickOption(idx)"
      >
        <!-- @slot 제안 목록 항목에 대한 slot -->
        <slot name="option" :option="option"></slot>
      </div>
      <div
        v-if="!optionList.length"
        :id="`${id}-option-no-result`"
        tabindex="-1"
        role="option"
        :aria-selected="`no-result` === selectedItem"
        :class="{
          [selectedClassName]:
            selectedClassName && `no-result` === selectedItem,
        }"
        @click="handleClickOption()"
      >
        <!-- @slot 제안 목록이 없을 경우 나타낼 항목에 대한 slot  -->
        <slot name="no-result" :keyword="value"></slot>
      </div>
      <div
        ref="status"
        role="status"
        aria-live="polite"
        aria-relevant="additions"
        :class="$style[`combobox__status`]"
      ></div>
    </div>
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
     * HTML input element에 대한 레이블
     */
    label: {
      type: String,
      required: true,
      default: `검색`,
    },
    /**
     * 레이블 가시적 노출 여부
     */
    showLabel: {
      type: Boolean,
      required: false,
      default: true,
    },
    /**
     * HTML placeholder 어트리뷰트에 해당
     */
    placeholder: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     * 제안 목록을 검색하기 위한 API function
     *
     */
    getList: {
      type: [Function, null],
      required: false,
      default: null,
    },
    /**
     * option 데이터에서 제안 목록의 레이블로 사용되는 프로퍼티명
     */
    labelPropName: {
      type: String,
      required: true,
      default: `label`,
    },
    /**
     * 탐색 중 항목 (초점을 얻고 있는 항목)에 적용할 클래스 명
     */
    selectedClassName: {
      type: String,
      required: false,
      default: `selected`,
    },
  },
  data() {
    return {
      id: `combobox-${this.$_uid}`,
      value: ``,
      isTyping: false,
      optionList: [],
      visibleOptionList: false,
      selectedItem: null,
    };
  },
  computed: {
    activatedDescendant() {
      if (!this.visibleOptionList || this.selectedItem === null) return false;
      return `${this.id}-option-${this.selectedItem}`;
    },
    useNoResultSlot() {
      return !!this.$scopedSlots[`no-result`];
    },
  },
  methods: {
    handleInput(event) {
      const { value } = event.target;
      this.setTypingStatus(true);
      this.value = value.trim();
    },
    handleSubmit() {
      const { value } = this;

      this.setTypingStatus(false);
      this.callApi(value);
    },
    handleEnter(event) {
      const { selectedItem } = this;

      if (selectedItem === null) this.handleSubmit();
      else this.chooseSelectedItem();
    },
    handleArrowDown() {
      const { isTyping, optionList, selectedItem } = this;
      if (isTyping) return;

      if (optionList.length) {
        /**
         * API에 의해 생성 된 option이 있을 경우 해당 option 사용
         */
        const size = optionList.length;
        const next = ((selectedItem ?? -1) + 1) % size;
        this.selectedItem = next;
      } else if (this.useNoResultSlot) {
        /**
         * API에 의해 생성 된 option이 없고, `no-result` slot 사용 시 해당 slot의 option을 사용
         */
        this.selectedItem = `no-result`;
      }
    },
    handleArrowUp() {
      const { isTyping, optionList, selectedItem } = this;

      if (isTyping) return;

      if (optionList.length) {
        /**
         * API에 의해 생성 된 option이 있을 경우 해당 option 사용
         */
        const size = optionList.length;
        const prev = ((selectedItem ?? size) + size - 1) % size;
        this.selectedItem = prev;
      } else if (this.useNoResultSlot) {
        /**
         * API에 의해 생성 된 option이 없고, `no-result` slot 사용 시 해당 slot의 option을 사용
         */
        this.selectedItem = `no-result`;
      }
    },
    handleEsc() {
      this.setTypingStatus(false);
      this.restoreOptionList();
      this.restoreStatus();
    },
    handleClickOption(idx) {
      this.chooseSelectedItem(idx);
    },
    callApi(keyword) {
      if (!keyword || !this.getList) return;

      this.getList(keyword).then(list => {
        this.setOptionList(list);
      });
    },
    setTypingStatus(status) {
      this.isTyping = status;
    },
    setOptionList(list) {
      this.optionList = [...list];
      this.visibleOptionList = true;
      setTimeout(() => {
        this.setStatus(list.length);
      }, 50);
    },
    restoreOptionList() {
      this.visibleOptionList = false;
      this.optionList = [];
      this.selectedItem = null;
    },
    setStatus(len) {
      if (len === null || !len === undefined) return;
      const { status } = this.$refs;
      const newEl = document.createElement(`p`);
      newEl.textContent = len
        ? `총 ${len}개의 제안 목록이 있습니다.`
        : `제안 목록이 없습니다.`;

      status.firstElementChild?.remove();
      status.append(newEl);
    },
    restoreStatus() {
      const { status } = this.$refs;
      status.firstElementChild?.remove();
    },
    getSelectedOption(idx) {
      const { labelPropName, value, optionList, selectedItem } = this;
      const noResultOption = {
        [labelPropName]: value,
      };

      return optionList[idx || selectedItem] || noResultOption;
    },
    chooseSelectedItem(idx) {
      const { labelPropName } = this;
      const option = this.getSelectedOption(idx);
      this.value = option?.[labelPropName];
      /**
       * 제안 목록 항목 선택 시 발생 <br>
       * `{...selectedOption}`, 제안 목록이 없을 경우 `{ [labelPropName]: String }`
       *
       * @event change
       */
      this.$emit(`change`, option);
      this.restoreOptionList();
      this.restoreStatus();
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
