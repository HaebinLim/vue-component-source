<template>
  <section :class="[`grid`, $style[`feedback`]]">
    <heading :rank="1" :class="$style[`heading`]">
      Feedback
      <span :class="$style[`heading__desc`]">
        더플랩 인사이트를 위한 한마디! <br />
        따끔한 충고도 좋습니다. 다양한 의견을 보내주세요.
      </span>
    </heading>
    <div :class="$style[`feedback__field`]">
      <SelectBox
        ref="category"
        :type="`large`"
        :context="`inline`"
        :show-label="true"
        :label="`카테고리`"
        :default-option="`유형`"
        :options="cateOption"
        :is-required="true"
        :class="$style[`feedback__category`]"
        @change="handleChangeCategory"
      />
      <InputField
        :context="`inline`"
        :label="`제목`"
        :show-label="true"
        :type="`text`"
        :maxlength="50"
        :value.sync="title.value"
        :is-required="title.required"
        :placeholder="`제목을 입력하세요`"
        :class="$style[`feedback__title`]"
      />
    </div>
    <div :class="$style[`feedback__field`]">
      <TextField
        :context="`default`"
        :subtitle="`피드백 내용`"
        :placeholder="`전해주실 내용을 적어주세요`"
        :max-length="1000"
        :use-counter="true"
        :value.sync="body.value"
        :is-required="body.required"
      />
    </div>
    <div :class="$style[`feedback__field`]">
      <AttachFiles
        ref="files"
        :is-required="false"
        @changed-files="handleChangeFiles"
      />
    </div>
    <div :class="$style[`feedback__field`]">
      <InputField
        :context="`block`"
        :label="`이메일 주소`"
        :show-label="true"
        :type="`text`"
        :value.sync="email.value"
        :required="email.required"
        :placeholder="`이메일 주소를 입력하세요`"
        aria-describedby="guide-email"
        :class="$style[`feedback__email`]"
      />
      <ul id="guide-email" :class="$style[`feedback__guide`]">
        <li>응답 받을 이메일 주소를 입력해주세요.</li>
        <li>익명 제출을 원하시면 적지 않으셔도 괜찮습니다.</li>
      </ul>
    </div>
    <div :class="[$style[`feedback__field`], $style[`feedback__terms`]]">
      <span :class="$style[`feedback__terms__title`]">
        개인정보 수집 동의
        <span :class="$style[`feedback__terms__subtitle`]"> 선택 </span>
      </span>
      <p :class="$style[`feedback__terms__desc`]">
        더플랩 인사이트는 이용자 문의를 처리하기 위해 이메일 정보를 수집하며,
        수집된 정보는 1년간 보관합니다. 회원님은 동의를 거부할 수 있으며, 거부
        시 응대지원이 원활하지 않을 수 있습니다.
      </p>
      <CheckBox
        ref="term"
        :context="`block`"
        :label="`더플랩 인사이트 개인정보 수집에 동의합니다`"
        :show-label="true"
        :value="``"
        @change="handleChangeTerm"
      />
    </div>
    <BoxButton
      :type="`button`"
      :context="`block`"
      :appearance="`basic`"
      :size="`x-large`"
      :label="`의견 보내기`"
      :class="$style[`feedback__submit`]"
      @click="handleSubmitFeedback"
    />
  </section>
</template>

<script>
import Heading from '@atoms/Heading';
import SelectBox from '@atoms/SelectBox/index';
import InputField from '@atoms/InputBox';
import TextField from '@molecules/TextField';
import AttachFiles from '@molecules/AttachFiles';
import CheckBox from '@atoms/CheckBox/index';
import BoxButton from '@atoms/Button/Box';

const categoryOption = [
  {
    text: `의견`,
    value: `VT001`,
  },
  {
    text: `불편사항`,
    value: `VT002`,
  },
  {
    text: `콘텐츠 아이디어`,
    value: `VT003`,
  },
  {
    text: `인터뷰 제안`,
    value: `VT004`,
  },
  {
    text: `리뷰 제안`,
    value: `VT005`,
  },
  {
    text: `제휴 제안`,
    value: `VT006`,
  },
  {
    text: `기타`,
    value: `VT00999`,
  },
];

const checkFilledUp = val => {
  if (typeof val === `string` && !val) {
    return false;
  }

  if (
    [Object, Array].includes((val || {}).constructor) &&
    !Object.entries(val || {}).length
  ) {
    return false;
  }

  return true;
};

export default {
  components: {
    Heading,
    SelectBox,
    InputField,
    TextField,
    AttachFiles,
    CheckBox,
    BoxButton,
  },
  props: {
    /**
     * Feedback 전송 API
     */
    sendFeedBack: {
      type: Function,
      required: true,
    },
  },
  data() {
    return {
      category: {
        required: true,
        value: null,
        validator(val) {
          if (!checkFilledUp(val)) {
            return {
              valid: false,
              message: `NOT_SELECTED_CATEGORY`,
            };
          } else {
            return {
              valid: true,
            };
          }
        },
      },
      title: {
        required: true,
        value: ``,
        validator(val) {
          if (!checkFilledUp(val)) {
            return {
              valid: false,
              message: `EMPTY_TITLE`,
            };
          } else {
            return {
              valid: true,
            };
          }
        },
      },
      body: {
        required: true,
        value: ``,
        validator(val) {
          if (!checkFilledUp(val)) {
            return {
              valid: false,
              message: `EMPTY_CONTENT`,
            };
          } else {
            return {
              valid: true,
            };
          }
        },
      },
      files: {
        required: false,
        value: [],
      },
      email: {
        required: true,
        value: ``,
      },
      term: {
        required: false,
        value: false,
      },
    };
  },
  computed: {
    cateOption() {
      return categoryOption;
    },
  },
  methods: {
    handleChangeCategory(val) {
      this.category.value = val;
    },
    handleChangeFiles(files) {
      this.files.value = [...files];
    },
    handleChangeTerm({ checked }) {
      this.term.value = checked;
    },
    handleSubmitFeedback() {
      const valid = this.validateFormData();
      const {
        category: { value: category },
        title: { value: title },
        body: { value: body },
        files: { value: files },
        email: { value: email },
        term: { value: term },
      } = this;
      if (valid && confirm(`전송하시겠습니까?`)) {
        this.sendFeedBack({
          category,
          title,
          body,
          files,
          email,
          term,
        })
          .then(() => {
            this.category.value = ``;
            this.title.value = ``;
            this.body.value = ``;
            this.email.value = ``;
            this.files.value = [];
            this.term.value = false;
            this.$refs.category.init();
            this.$refs.files.init();
            this.$refs.term.init();
            alert(`전송했습니다! \n의견 주셔서 감사합니다.`);
          })
          .catch(error => {
            const msg = error?.detail || this.$msg('feedback.error.default');
            alert(msg);
          });
      }
    },
    validateFormData() {
      const { category, title, body, files, email, term } = this;
      const validator = ({ validator, value }) => {
        if (validator) {
          const test = validator(value);
          test.valid || alert(this.$msg(`feedback.error.${test?.message}`));
          return test.valid;
        }
        return true;
      };
      return [category, title, body, files, email, term].every(validator);
    },
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
