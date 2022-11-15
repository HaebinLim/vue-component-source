<template>
  <section :class="[`grid`, $style[`uptdate-profile`]]">
    <div :class="$style[`uptdate-profile__title-wrap`]">
      <Heading
        :rank="1"
        :class="$style[`uptdate-profile__title`]"
        :text-content="pageTitle"
      />
      <p v-if="!isEditMode" :class="$style[`uptdate-profile__title__sub`]">
        해당 정보는 전부 필수 정보 입니다.<br />
        정보를 바탕으로 유익한 정보를 전달드리겠습니다.
      </p>
    </div>
    <div :class="$style[`uptdate-profile__form`]">
      <div
        :class="[
          $style[`uptdate-profile__item`],
          $style[`uptdate-profile__item--btn-wrap`],
        ]"
      >
        <InputBox
          :label="`닉네임`"
          :placeholder="`닉네임을 입력해주세요`"
          :maxlength="10"
          :type="`text`"
          :is-required="true"
          :value.sync="infoNickName"
          :class="$style[`uptdate-profile__input-txt`]"
          :status="nickNameStatus"
          :feedback-msg="nickNameFeedbackMsg"
          @update:value="changeNickName"
        />
        <BoxButton
          :class="$style[`uptdate-profile__input-btn`]"
          :type="`button`"
          :appearance="`basic`"
          :inactive="nickNameBtnInactive"
          :size="`large`"
          :label="`중복확인`"
          @click="duplicateCheck"
        />
        <ul :class="$style[`uptdate-profile__item__subtxt`]">
          <li>한글 기준 최소 2자, 최대 10자까지 사용 가능합니다.</li>
          <li>한글, 영어, 숫자, 특수문자 사용 가능합니다.</li>
          <li>사용 가능 특수문자 !@#$%*+=_-</li>
        </ul>
      </div>
      <div :class="$style[`uptdate-profile__item`]">
        <RadioButtonGroup
          :items="jobStatusOptions"
          :name="`radioEmployed`"
          :group-label="`현 재직 상태`"
          :is-required="true"
          @change="changeJobStatus"
        />
      </div>
      <div
        :class="[
          $style[`uptdate-profile__item`],
          $style[`uptdate-profile__item--btn-wrap`],
          $style[`uptdate-profile__item--company`],
        ]"
      >
        <InputBox
          :label="`소속 회사명`"
          :placeholder="`소속 회사를 검색해주세요.`"
          :type="`text`"
          :is-required="true"
          :value="infoCompany"
          :status="isEmployed ? `` : `disabled`"
          :readonly="true"
          :class="$style[`uptdate-profile__input-txt`]"
        />
        <BoxButton
          :class="$style[`uptdate-profile__input-btn`]"
          :type="`button`"
          :disabled="!isEmployed"
          :appearance="`basic`"
          :size="`large`"
          :label="`검색`"
          @click="handleClickSearch"
        />
      </div>
      <div :class="$style[`uptdate-profile__item`]">
        <SelectBox
          :type="`large`"
          :show-label="true"
          :label="`직무`"
          :context="`block`"
          :default-option="`직무를 선택해주세요`"
          :is-required="true"
          :options="jobCategoryOptions"
          :disabled="!isEmployed"
          :value="infoJobCategory"
          @change="changeJobCategory"
        />
      </div>
      <div :class="$style[`uptdate-profile__item`]">
        <InputBox
          :label="`직책`"
          :context="`block`"
          :placeholder="`직책을 입력해주세요`"
          :maxlength="10"
          :type="`text`"
          :is-required="true"
          :value.sync="infoJobPosition"
          :status="isEmployed ? `` : `disabled`"
          @update:value="changeJobPosition"
        />
        <ul :class="$style[`uptdate-profile__item__subtxt`]">
          <li>최대 10자까지 사용 가능합니다.</li>
        </ul>
      </div>
      <div :class="$style[`uptdate-profile__agreement`]">
        <div :class="$style[`uptdate-profile__agreement__tit`]">
          광고성 정보 수신 동의
          <span :class="$style[`uptdate-profile__agreement__required`]">
            선택
          </span>
        </div>
        <p :class="$style[`uptdate-profile__agreement__desc`]">
          더플랩 인사이트에서 보내는 특별한 제안 및 뉴스레터, 웨비나, 연구보고서
          등에 대한 소식을 이메일, 문자 메시지 등으로 받게 됩니다. 수신 여부는
          프로필 수정에서 언제든지 변경할 수 있습니다.
        </p>
        <div :class="$style[`uptdate-profile__agreement__items`]">
          <CheckBox
            :label="`전체선택`"
            :value="`marketingAgreement`"
            :checked="selectAll"
            @change="handleSelectAll"
          />
          <CheckBox
            v-for="{ text, value, slug } in marketingOptions"
            :key="value"
            :label="text"
            :value="value"
            :checked="getAgreeCheckState(slug)"
            @change="handleChangeSelected"
          />
        </div>
      </div>
      <BoxButton
        :type="`submit`"
        :context="`block`"
        :appearance="`basic`"
        :inactive="!submitBtnInactive"
        :size="`x-large`"
        :label="submitText"
        :class="$style[`uptdate-profile__submit`]"
        @click="handleSubmitProfile"
      />
    </div>
    <modal
      v-if="showSearchModal"
      :role="`dialog`"
      :label="`소속 회사명`"
      :label-ref="false"
      :is-modal="true"
      @destroyed="handleModalDestroy"
    >
      <div :class="$style[`search`]">
        <combo-box
          :label="`소속 회사명`"
          :placeholder="`키워드 입력 후 Enter로 검색`"
          :get-list="companyListApi"
          :label-prop-name="`label`"
          :maxlength="companyNameMaxlength"
          @change="changeCompanyName"
        >
          <template #option="{ option }">
            <div :class="$style[`search__result`]">
              <div
                :class="$style[`search__result__title`]"
                v-html="option.label"
              ></div>
              <div :class="$style[`search__result__address`]">
                {{ option.address }}
              </div>
            </div>
          </template>
          <template #no-result="{ keyword }">
            <div :class="$style[`search__no-result`]">
              <strong :class="$style[`search__no-result__keyword`]">{{
                keyword
              }}</strong>
              입력
              <p :class="$style[`search__no-result__text`]">
                일치하는 회사명이 없습니다.
              </p>
            </div>
          </template>
        </combo-box>
      </div>
    </modal>
  </section>
</template>
<script>
import Heading from '@atoms/Heading/index';
import InputBox from '@atoms/InputBox/index';
import CheckBox from '@atoms/CheckBox';
import RadioButtonGroup from '@molecules/RadioButtonGroup/index';
import SelectBox from '@atoms/SelectBox/index';
import BoxButton from '@atoms/Button/Box/index';
import Modal from '@organisms/Dialog';
import ComboBox from '@molecules/ComboBox/index';

const validateAvailLength = (val, min) => min && val.length < min;
const validateAvailChar = (regExp, val) => regExp.test(val);
const validateEmptyString = (regExp, val) => val.search(regExp) !== -1;

export default {
  components: {
    Heading,
    InputBox,
    CheckBox,
    RadioButtonGroup,
    SelectBox,
    BoxButton,
    Modal,
    ComboBox,
  },
  props: {
    /**
     *  프로필 수정 모드
     */
    isEditMode: {
      type: Boolean,
      required: false,
      default: false,
    },
    /**
     *  닉네임
     */
    userNickName: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     *  재직 상태
     */
    jobStatus: {
      type: String,
      required: false,
      default: `work`,
    },
    /**
     *  소속 회사명
     */
    companyName: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     *  직무
     */
    jobCategory: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     *  직책
     */
    jobPosition: {
      type: String,
      required: false,
      default: ``,
    },
    /**
     *  이메일 수신 동의
     */
    agreeSendEmail: {
      type: Boolean,
      required: false,
      default: false,
    },
    /**
     *  문자 수신 동의
     */
    agreeSendSms: {
      type: Boolean,
      required: false,
      default: false,
    },
    /**
     * 닉네임 중복확인 API 호출 함수
     */
    duplicateCheckApi: {
      type: Function,
      required: true,
    },
    /**
     * 기업명 API 호출 함수
     */
    companyListApi: {
      type: Function,
      required: true,
    },
  },
  data() {
    return {
      infoNickName: null,
      infoJobStatus: null,
      infoCompany: null,
      infoJobCategory: null,
      infoJobPosition: null,
      agreeEmail: false,
      agreeSms: false,
      nickNameBtnInactive: true,
      nickNameStatus: ``,
      nickNameFeedbackMsg: ``,
      nickNameReadonly: false,
      jobStatusOptions: [
        { label: `재직중`, value: `work` },
        { label: `구직중`, value: `s_work` },
        { label: `학생`, value: `student` },
      ],
      jobCategoryOptions: [
        { text: `인사`, value: `OCTP01` },
        { text: `영업/마케팅`, value: `OCTP02` },
        { text: `경영지원`, value: `OCTP03` },
        { text: `연구개발`, value: `OCTP04` },
        { text: `생산/품질`, value: `OCTP05` },
        { text: `IT`, value: `OCTP06` },
        { text: `기타`, value: `OCTP07` },
      ],
      marketingOptions: [
        {
          text: `이메일`,
          value: `RCV_E01`,
          slug: `agreeEmail`,
        },
        {
          text: `문자 메시지`,
          value: `RCV_S01`,
          slug: `agreeSms`,
        },
      ],
      selectAll: false,
      showSearchModal: false,
      companyNameMaxlength: 20,
    };
  },
  computed: {
    pageTitle() {
      return this.isEditMode ? `프로필 수정` : `프로필 입력`;
    },
    submitText() {
      return this.isEditMode ? `저장` : `완료`;
    },
    isEmployed() {
      return this.infoJobStatus === `work`;
    },
    checkJobStatus() {
      return this.jobStatusOptions.map(option => ({
        ...option,
        checked: option.value === this.infoJobStatus,
      }));
    },
    submitBtnInactive() {
      const nickNameCheck =
        this.nickNameStatus === 'verified' && this.infoNickName;
      const employedCheck = this.isEmployed
        ? !!this.infoCompany && !!this.infoJobCategory && !!this.infoJobPosition
        : true;
      return nickNameCheck && employedCheck;
    },
  },
  created() {
    this.infoNickName = this.userNickName;
    this.infoJobStatus = this.jobStatus;
    this.infoCompany = this.companyName;
    this.infoJobCategory = this.jobCategory;
    this.infoJobPosition = this.jobPosition;
    this.agreeEmail = this.agreeSendEmail;
    this.agreeSms = this.agreeSendSms;
    this.jobStatusOptions = this.checkJobStatus;
    if (this.isEditMode) {
      this.nickNameStatus = `verified`;
      this.selectAll = this.agreeEmail && this.agreeSms;
    }
  },
  methods: {
    setNickFeedBack({ status, msg, btnIntactive = null }) {
      this.nickNameStatus = status;
      this.nickNameFeedbackMsg = msg;
      this.nickNameBtnInactive =
        btnIntactive !== null ? btnIntactive : status !== `verified`;
    },
    getAgreeCheckState(slug) {
      return this[slug];
    },
    duplicateCheck() {
      const nickName = this.infoNickName;

      this.duplicateCheckApi(nickName)
        .then(response => {
          this.setNickFeedBack({
            status: `verified`,
            msg: ``,
          });
          this.infoNickName = nickName;
        })
        .catch(error => {
          const msg = error?.response?.data?.detail || this.$msg(`default`);

          this.setNickFeedBack({
            status: `error`,
            msg,
          });
          this.infoNickName = this.userNickName || nickName;
        });
    },
    changeNickName() {
      this.setNickFeedBack({
        status: `error`,
        msg: `중복 확인을 해주세요.`,
        btnIntactive: false,
      });

      if (this.infoNickName === ``) {
        this.setNickFeedBack({
          status: ``,
          msg: ``,
        });
        return false;
      }

      if (validateEmptyString(/\s/, this.infoNickName)) {
        this.setNickFeedBack({
          status: `error`,
          msg: `공백을 제거해 주세요.`,
        });
        return false;
      }

      if (
        !validateAvailChar(
          /^[ㄱ-ㅎㅏ-ㅣ가-힣a-zA-Z0-9!@#$%*+=_-]*$/,
          this.infoNickName,
        )
      ) {
        this.setNickFeedBack({
          status: `error`,
          msg: `한글, 영어, 숫자 및 특수 문자 !@#$%*+=_-만 사용 가능합니다.`,
        });
        return false;
      }

      if (validateAvailLength(this.infoNickName, 2)) {
        this.setNickFeedBack({
          status: `error`,
          msg: `한글 기준 최소 2자, 최대 10자까지 입력 가능합니다.`,
        });
        return false;
      }

      if (this.userNickName === this.infoNickName) {
        this.setNickFeedBack({
          status: `verified`,
          msg: ``,
          btnIntactive: true,
        });
      }
    },
    changeJobStatus(payload) {
      this.infoJobStatus = payload.value;
    },
    changeCompanyName(payload) {
      this.showSearchModal = false;
      this.infoCompany = payload.label.slice(0, this.companyNameMaxlength);
    },
    changeJobCategory(payload) {
      this.infoJobCategory = payload;
    },
    changeJobPosition(payload) {
      this.infoJobPosition = payload;
    },
    handleSelectAll({ checked }) {
      this.selectAll = checked;
      if (checked) {
        this.agreeEmail = true;
        this.agreeSms = true;
      } else {
        this.agreeEmail = false;
        this.agreeSms = false;
      }
    },
    handleChangeSelected({ checked, value }) {
      const getTarget = () => {
        const idx = this.marketingOptions.findIndex(
          element => element.value === value,
        );
        return {
          0: `agreeEmail`,
          1: `agreeSms`,
        }[idx];
      };
      this[getTarget()] = checked;

      this.selectAll = this.agreeEmail && this.agreeSms;
    },
    handleClickSearch() {
      this.showSearchModal = true;
    },
    handleModalDestroy() {
      this.showSearchModal = false;
      /**
       * 팝업이 닫힐 때 발생
       *
       * @event closed
       */
      this.$emit(`closed`);
    },
    handleSubmitProfile() {
      if (!this.isEmployed) {
        this.infoCompany = ``;
        this.infoJobCategory = ``;
        this.infoJobPosition = ``;
      }
      /**
       * 저장/완료 버튼 클릭 시
       *
       * @event update
       */
      this.$emit(`update`, {
        userNickName: this.infoNickName,
        jobStatus: this.infoJobStatus,
        companyName: this.infoCompany,
        jobCategory: this.infoJobCategory,
        jobPosition: this.infoJobPosition,
        agreeSendEmail: this.agreeEmail ? 'Y' : 'N',
        agreeSendSms: this.agreeSms ? 'Y' : 'N',
      });
    },
  },
};
</script>
<style lang="scss" module src="./index.scss"></style>
