<template>
  <div class="filter-checkbox">
    <strong class="item-title">{{ label }}</strong>
    <div class="d-flex">
      <ow-button
        class="ow-filter-button"
        :class="{ active }"
        @click="clickToggleAll"
      >
        전체
      </ow-button>
      <ow-checkbox
        :options="options"
        v-model="checkedValue"
        @update:modelValue="changeValue"
      ></ow-checkbox>
    </div>
  </div>
</template>

<script>
import { ref, computed } from 'vue';

export default {
  name: 'FilterCheckbox',
  props: {
    all: {
      type: Boolean,
    },
    label: {
      type: String,
    },
    options: {
      type: Array,
      default:() => {
        return [];
      }
    },
  },
  emits: ['changeValue'],
  setup(props, { emit }){
    const checkedValue = ref([]);

    const active = computed(()=>{
      return props.options.length === checkedValue.value.length;
    });

    const clickToggleAll = () => {
      if(active.value) {
        checkedValue.value = []
      } else {
        props.options.forEach(({ value }) => {
          checkedValue.value.push(value);
        });
      }
      emit('changeValue', checkedValue.value);
    }

    const changeValue = (newValue) => {
      emit('changeValue', newValue)
    }

    if(props.all) {
      props.options.forEach(({ value }) => {
        checkedValue.value.push(value);
      });
    } else {
      props.options.forEach(({ value, checked }) => {
        if(checked) checkedValue.value.push(value);
      });
    }

    return {
      active,
      checkedValue,
      changeValue,
      clickToggleAll,
    }
  }
}
</script>

<style lang="scss" scoped>
.filter-checkbox {
  display: flex;
  align-items: center;
  gap: 0 10px;
  .d-flex {
    flex-wrap: wrap;
    align-items: center;
    padding: 0 8px 0 4px;
    border-radius: 2px;
    background: #e1e6ea;
  }
  .ow-filter-button {
    background: #fff;
    &.active {
      background-color: #4e95f5 !important;
      border: 1px solid #166ce3 !important;
      color: #fff !important;
    }
  }
}
</style>