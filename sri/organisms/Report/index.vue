<template>
  <modal
    ref="dialog"
    :role="`dialog`"
    :label="false"
    :label-ref="`heading-dialog-${$_uid}`"
    :is-modal="true"
    @destroyed="handleDestroy"
  >
    <div :class="$style[`report`]">
      <Heading
        :id="`heading-dialog-${$_uid}`"
        :rank="headingRank"
        :text-content="`신고하기`"
        :class="$style[`report__heading`]"
      />
      <RadioButtonGroup
        :columns="2"
        :items="reportTypes"
        :name="`report-type`"
        :group-label="`신고 유형`"
        :heading-rank="headingRank + 1"
        :hide-heading="false"
        :class="$style[`report__field`]"
        @change="handleChangeType"
      />
      <TextField
        :context="`default`"
        :use-button="false"
        :subtitle="`신고사유`"
        :is-required="false"
        :placeholder="`전해주실 내용을 적어주세요.`"
        :max-length="1000"
        :use-counter="true"
        :value.sync="reason"
        :class="[$style[`report__field`], $style[`report__field--last`]]"
      />
      <div :class="$style[`report__controls`]">
        <BoxButton
          :type="`button`"
          :context="`block`"
          :appearance="`cancel`"
          :size="`large`"
          :label="`취소`"
          :class="$style[`report__btn`]"
          @click="handleCancel"
        />
        <BoxButton
          :type="`button`"
          :context="`block`"
          :appearance="`basic`"
          :size="`large`"
          :label="`확인`"
          :class="$style[`report__btn`]"
          @click="handleSubmit"
        />
      </div>
    </div>
  </modal>
</template>

<script>
import Modal from '@organisms/Dialog';
import Heading from '@atoms/Heading';
import RadioButtonGroup from '@molecules/RadioButtonGroup';
import TextField from '@molecules/TextField';
import BoxButton from '@atoms/Button/Box';

const REPORT_TYPES = [
  {
    label: `영리 목적/홍보성`,
    value: `DR001`,
  },
  {
    label: `불법 정보`,
    value: `DR002`,
  },
  {
    label: `음란/선정성`,
    value: `DR003`,
  },
  {
    label: `욕설/인신공격`,
    value: `DR004`,
  },
  {
    label: `개인정보노출`,
    value: `DR005`,
  },
  {
    label: `같은 내용의 반복 게시(도배)`,
    value: `DR006`,
  },
  {
    label: `허위사실유포`,
    value: `DR007`,
  },
  {
    label: `기타`,
    value: `DR008`,
  },
];

const validator = ({ type, reason, isLoggedIn }) => {
  return new Promise((resolve, reject) => {
    if (!isLoggedIn) reject(new Error(`REQUIRED_LOGIN`));
    ![undefined, null, ``].includes(type)
      ? resolve({ type, reason })
      : reject(new Error(`NO_SELECTED_TYPE`));
  });
};

export default {
  components: {
    Modal,
    Heading,
    RadioButtonGroup,
    TextField,
    BoxButton,
  },
  props: {
    /**
     * 대화상자 제목 레벨
     */
    headingRank: {
      type: Number,
      required: true,
    },
    /**
     * 신고 API 함수
     */
    submitReport: {
      type: Function,
      required: true,
    },
    /**
     * 신고 대상 댓글 ID
     */
    targetId: {
      type: [String, Number],
      required: true,
      validator(id) {
        return id !== ``;
      },
    },
    /**
     * 로그인 여부
     */
    isLoggedIn: {
      type: Boolean,
      required: true,
      default: false,
    },
  },
  data() {
    return {
      type: null,
      reason: ``,
    };
  },
  computed: {
    reportTypes() {
      return REPORT_TYPES;
    },
  },
  methods: {
    handleDestroy() {
      /**
       * 팝업이 닫힐 때 발생
       *
       * @event closed
       */
      this.$emit(`closed`);
    },
    handleChangeType({ value }) {
      this.type = value;
    },
    handleCancel() {
      this.$refs.dialog.handleClose();
    },
    handleSubmit() {
      const { type, reason, targetId, isLoggedIn, submitReport } = this;

      validator({ type, reason, isLoggedIn })
        .then(() => {
          return submitReport({ type, reason, targetId });
        })
        .then(() => {
          alert(this.$msg(`report.success`));
          this.$refs.dialog.handleClose();
        })
        .catch(error => {
          /**
           * 신고 실패 시 발생 <br>
           * payload로 error object 전달
           *
           * @event submit-failed
           */
          this.$emit(`submit-failed`, error);
          const msg = error?.detail || this.$msg(`report.error.default`);
          alert(msg);
        });
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
