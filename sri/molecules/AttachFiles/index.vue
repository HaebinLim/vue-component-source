<template>
  <div
    role="group"
    :aria-labelledby="`label-${uid}`"
    :aria-describedby="`guide-${uid}`"
    :class="$style[`attach`]"
  >
    <div :id="`label-${uid}`" :class="$style[`attach__header`]">
      <span :class="$style[`attach__label`]"> 첨부파일 </span>
      <span
        v-if="!$isNull(isRequired)"
        aria-hidden="true"
        :class="$style[`attach__required`]"
      >
        {{ isRequired | strRequired }}
      </span>
    </div>

    <InputFile :disabled="reachedFull" @change="handleSelectedFile" />

    <ul :class="$style[`attach__files`]">
      <template v-for="{ name, id } in files">
        <li :key="id" :class="$style[`attach__files__entry`]">
          <span :class="$style[`attach__files__name`]">
            {{ name }}
          </span>
          <BoxButton
            :type="`button`"
            :context="`inline`"
            :appearance="`icon`"
            :size="`small`"
            :label="`삭제 - ${name}`"
            :icon="`error`"
            :class="$style[`attach__files__delete`]"
            @click="handleRemoveFile(id)"
          />
        </li>
      </template>
    </ul>
    <div :id="`guide-${uid}`" :class="$style[`attach__guide`]">
      최대 {{ maxNumberOfFiles }}개 / {{ maxAllowedSize }} 미만
    </div>
  </div>
</template>

<script>
import InputFile from '@atoms/InputFile';
import BoxButton from '@atoms/Button/Box';

const byteToMB = size => {
  if (!Number.isInteger(size)) return NaN;
  return (size / 1024 / 1024).toFixed(2).replace(/\.0+$/, ``) + `MB`;
};

const checkFileSize = file => {
  return new Promise((resolve, reject) => {
    const { size } = file;
    if (size < MAX_ALLOWED_SIZE) resolve(file);
    else {
      const err = new Error(`EXCEED_ALLOWED_SIZE`);
      err.data = {
        interpolation: {
          MAX_ALLOWED_SIZE: byteToMB(MAX_ALLOWED_SIZE),
        },
      };
      reject(err);
    }
  });
};

const checkFileFormat = file => {
  return new Promise((resolve, reject) => {
    const ext = file?.name?.split('.').pop();

    if (DISALLOWED_EXTS.includes(ext)) reject(new Error(`NOT_ALLOWED_FORMAT`));
    else resolve(file);
  });
};

const MAX_NUMBER_OF_FILES = 3;
const MAX_ALLOWED_SIZE = 5 * 1024 * 1024;
const DISALLOWED_EXTS = [
  `ade`,
  `adp`,
  `bat`,
  `chm`,
  `cmd`,
  `com`,
  `cpl`,
  `exe`,
  `hta`,
  `ins`,
  `isp`,
  `jse`,
  `lib`,
  `lnk`,
  `mde`,
  `msc`,
  `msp`,
  `mst`,
  `pif`,
  `scr`,
  `sct`,
  `shb`,
  `sys`,
  `vb`,
  `vbe`,
  `vbs`,
  `vxd`,
  `wsc`,
  `wsf`,
  `wsh`,
];

export default {
  filters: {
    strRequired(val) {
      return val ? `필수` : `선택`;
    },
  },
  components: {
    InputFile,
    BoxButton,
  },
  props: {
    /**
     * 파일 전송 API 함수 <br>
     * `{id, name}`을 resolve 하는 Promise 반환
     */
    sendFile: {
      type: Function,
      required: true,
    },
    /**
     * 파일 삭제 API 함수
     */
    removeFile: {
      type: Function,
      required: true,
    },
    /**
     * 필수 항목 여부
     */
    isRequired: {
      type: [Boolean, null],
      required: false,
      default: null,
    },
  },
  data() {
    return {
      uid: this.$_uid,
      files: [],
    };
  },
  computed: {
    maxAllowedSize() {
      return byteToMB(MAX_ALLOWED_SIZE);
    },
    maxNumberOfFiles() {
      return MAX_NUMBER_OF_FILES;
    },
    reachedFull() {
      return this.files.length >= MAX_NUMBER_OF_FILES;
    },
  },
  methods: {
    handleSelectedFile(file) {
      checkFileSize(file)
        .then(file => checkFileFormat(file))
        .then(file => this.sendFile(file))
        .then(result => {
          /**
           * 파일 첨부 성공 시 발생
           *
           * @event upload-success
           */
          this.$emit(`upload-success`);
          this.files.push(result);
        })
        .catch(error => {
          const { message, data } = error;
          /**
           * 파일 첨부 혹은 전송 실패 시 발생 <br>
           * payload로 error object 전달
           *
           * @event upload-failed
           */
          this.$emit(`upload-failed`, error);
          alert(this.$msg(`attach_file.error.${message}`, data));
        });
    },
    handleRemoveFile(removeFileId) {
      this.removeFile(removeFileId)
        .then(result => {
          this.files = this.files.filter(({ id }) => id !== removeFileId);
          /**
           * 파일 삭제 성공 시 발생
           *
           * @event remove-success
           */
          this.$emit(`remove-success`);
        })
        .catch(error => {
          const { message, data } = error;
          /**
           * 파일 삭제 실패 시 발생
           *
           * @event remove-failed
           */
          this.$emit(`remove-failed`);
          alert(this.$msg(`attach_file.error.${message}`, data));
        });
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
