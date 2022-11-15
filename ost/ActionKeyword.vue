<template>
  <div class="info-keyword">
    <template v-if="editMode">
      <OwInput v-model="text" @keyup.enter="changeKeyword" v-bind="$attrs" />
      <ow-button class="type-icon file-blue" @click="changeKeyword"><span class="sr-only">저장</span></ow-button>
    </template>
    <template v-else>
      <span class="txt-keyword">{{ keyword }}</span>
      <ow-button class="type-icon editor-blue" @click="editMode = true"><span class="sr-only">수정</span></ow-button>
    </template>
  </div>
</template>
  
<script>
import { ref, computed } from 'vue';
  
export default {
  name: 'ActionKeyword',
  props: {
    keyword: {
      type: String,
    },
  },
  emits: ['changeKeyword'],
  setup(props, { emit }){
    let editMode = ref(false);
    let value;
    let text = computed({
      get: () => props.keyword,
      set: newValue => value = newValue,
    });
    const changeKeyword = () => {
      editMode.value = false;
      emit('changeKeyword', value);
    }

    return {
      text,
      editMode,
      changeKeyword,
    }
  }
}
</script>

<style lang="scss" scoped>
.info-keyword {
  display: flex;
  align-items: center;
  gap: 0 4px;
  > .d-inline-flex {
    width: var(--width, calc(100% - 20px));
  }
  .txt-keyword {
    padding: 4px 0;
    color: #176de2;
    line-height: 16px;
  }
  .ow-btn.editor-blue {
    flex: 0 0 auto;
    align-self: flex-start;
    margin: 4px 0;
  }
}
</style>