<template>
  <div class="filter-accordion">
    <div class="d-flex">
      <template v-for="{ text, value }, idx in buttons" :key="idx" >
        <template v-if="type === 'expanded'" >
          <ow-button 
            v-if="chkIdx === idx || isExpanded" 
            class="type-base color-dark" 
            :class="{ active : chkIdx === idx }"
            @click="[changeMain(value, idx), clickExpand(idx)]"
          >
            {{ text }}
          </ow-button>
        </template>
        <template v-else>
          <ow-button 
            class="type-base color-dark" 
            :class="{ active : chkIdx === idx }" 
            @click="changeMain(value, idx)"
          >
            {{ text }}
          </ow-button>
        </template>
        <template v-if="buttons[chkIdx].items.length">
          <div 
            v-if="type === 'expanded' ? chkIdx === idx && !isExpanded : chkIdx === idx" 
            :class="['ow-filter', { 'order-last' : type === 'expanded' }]"
          >
            <ul class="ow-filter-list">
              <template 
                v-for="({ text, value, disabled = false, addClass }, index) in buttons[chkIdx].items" 
                :key="value"
              >
                <li :class="addClass">
                  <input
                    type="radio"
                    :id="`${unique}-${index}`"
                    :name="unique"
                    :value="value"
                    :disabled="disabled"
                    v-model="chkItem"
                    @update:modelValue="changeSub(idx, ...arguments)"
                  />
                  <label :for="`${unique}-${index}`" class="ow-filter-button">
                    {{ text }}
                  </label>
                </li>
              </template>
            </ul>
          </div>
        </template>
      </template>
    </div>
    <OwRadioButton
      v-if="options.length"
      :items="options" 
      :light="true" 
      v-model="chkValue"
      @update:modelValue="changeValue"
    />
  </div>
</template>

<script>
import { ref, computed } from 'vue';
import { expando } from '@/utils';

export default {
  name: 'FilterAccordion',
  props: {
    type: {
      type: String,
      default: 'accordion',
      validator(type) {
        return ['expanded', 'accordion'].includes(type);
      },
    },
    checkedIdx: {
      type: Number,
      default: 0,
    },
    buttons: {
      type: Array,
      default:() => {
        return [];
      }
    },
    options: {
      type: Array,
      default:() => {
        return [];
      }
    },
  },
  emits: ['changeItem', 'changeValue'],
  setup(props, { emit }){
    const unique = expando('ow-filter-radio');
    const chkIdx = ref('');
    const chkItem = ref('');
    const chkValue = ref('');
    let isExpanded = ref(false);

    const getIdx = (btnIdx) => {
      let idx = 0;
      if(props.buttons[btnIdx].items.length) {
        props.buttons[btnIdx].items.forEach((element, i) => {
          if(element.value == chkItem.value) {
            idx = i;
          }
        });
      }
      return idx;
    }
    const getItem = (btnIdx) => {
      return props.buttons[btnIdx].items.length && props.buttons[btnIdx].items[getIdx(btnIdx)].value;
    }
    const clickExpand = () => {
      if(props.type === 'expanded') {
        isExpanded.value = !isExpanded.value;
      }
    }
    const changeMain = ( value, idx) => {
      chkIdx.value = idx;
      chkItem.value = getItem(idx);
      emit('changeItem', {
        'index': idx, 
        'value': value, 
        'item': chkItem.value 
      });
    }
    const changeSub = (idx, payload) => {
      chkItem.value = payload.chkItem;
      emit('changeItem', {
        'index': idx, 
        'value': props.buttons[idx].value, 
        'item': payload.chkItem
      });
    }
    const changeValue = (newValue) => {
      chkValue.value = newValue;
      emit('changeValue', newValue);
    };

    chkIdx.value = props.checkedIdx;
    chkItem.value = getItem(chkIdx.value);
    props.options.forEach(({ value, checked }) => {
      if(checked) chkValue.value = value;
    });

    return {
      unique,
      chkItem,
      chkIdx,
      chkValue,
      isExpanded,
      changeMain,
      changeSub,
      changeValue,
      clickExpand,
    }
  }
}
</script>

<style lang="scss" scoped>
.filter-accordion {
  display: flex;
  gap: 0 6px;
  .ow-btn {
    margin: 0 1px 0 0;
    padding: 7px 8px;
    height: 24px;
    &.active {
      border: 1px solid #176de2;
      background: #4e95f5;
    }
  }
  .ow-filter {
    margin-right: 2px;
    &.order-last {
      order: 1;
    }
    .inactive {
      input:not(:checked) {
        + .ow-filter-button {
          color: gray;
        }
      }
    }
    input:disabled {
      + .ow-filter-button {
        color: gray;
        &:hover {
          background: #e1e6ea;
        }
      }
    }
  }
  :deep {
    .radio-button-group {
      flex: 0 0 auto;
    }
  }
}
</style>