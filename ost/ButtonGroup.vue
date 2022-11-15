<template>
  <div class="d-flex">
    <template v-for="(btn, idx) in btns" :key="idx">
    <div>
      <ow-button 
        v-for="(b, i) in btn" 
        :key="b" 
        :class="['type-group', {'active' : typeof index === 'string' ? index === b : index === (lenSum[idx]+i)}]" 
        @click="clickButton(lenSum[idx]+i, b)">{{ b }}</ow-button>
    </div>
    </template>
  </div>
</template>

<script>
import { ref } from 'vue';

export default {
  name: 'ButtonGroup',
  props: {
    btns: {
      type: Array,
      default: () => {
        return [];
      }
    },
    activeIdx: {
      type: [String, Number],
    }
  },
  emits: ['clickButton'],
  setup(props, { emit }){
    let index = ref('');
    let lenSum = ref([0]);

    index.value = props.activeIdx;

    let len = [];
    props.btns.forEach((btn, idx) => {
      len.push(btn.length);
      let sum = 0;
      for(let count = 0; count <= idx; count++){
        sum += Number(len[count]);
      }
      lenSum.value.push(sum);
    });
    
    const clickButton = (idx, text) => {
      index.value = typeof index.value === 'string' ? text : idx;
      emit('clickButton', {
        index: idx,
        text
      });
    }
    
    return {
      index,
      lenSum,
      clickButton,
    };
  },
}
</script>

<style lang="scss" scoped>
  .d-flex {
    gap: 0 10px;
  }
  .ow-btn.type-group {
    &:first-of-type:last-of-type {
      border-radius: 2px;
      border-right: 1px solid #bababa;
    }
    &.active {
      &:first-of-type:last-of-type {
        border-color: #2461b3;
      }
    }
  }
</style>