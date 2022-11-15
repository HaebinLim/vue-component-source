<template>
  <div :class="[`grid`, $style[`video__container`]]">
    <div :class="[`grid--full`, $style[`video__cover`]]">
      <Video
        :youtube-id="videoInfo.videoId"
        :youtube-title="`${$parent.postInfo.title} 영상`"
        :class="$style[`video`]"
      />
    </div>
    <div :class="[$style[`video__desc`]]" v-html="videoInfo.desc"></div>
  </div>
</template>

<script>
import Video from '@atoms/Youtube';

export default {
  components: {
    Video,
  },
  props: {
    videoInfo: {
      type: Object,
      required: true,
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
