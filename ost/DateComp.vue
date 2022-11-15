<template>
  <div class="table-status">
    <fieldset class="ow-filter">
      <button type="button" class="ow-filter-btn-move prev" @click="this[`prev${dateType}`]">
        <span class="sr-only">이전</span>
      </button>
      <span class="filter-display">{{ this[`selected${dateType}`] }}</span>
      <button type="button" class="ow-filter-btn-move next" @click="this[`next${dateType}`]">
        <span class="sr-only">다음</span>
      </button>
    </fieldset>
    <ow-button class="type-state ml-4" @click="getToday">오늘</ow-button>
  </div>
</template>

<script>
//store
import { createNamespacedHelpers } from 'vuex';
const { mapMutations } = createNamespacedHelpers('sal/sal');

export default {
  name: 'DateComp',
  props: {
    type: {
      type: String,
      default: `day`,
      validator(ty) {
        return [`day`, `week`, `month`, `year`].includes(ty);
      },
    },
  },
  data() {
    return {
      today : '',
      selectedDay: '',
      selectedWeek: '',
      selectedMonth: '',
      selectedYear: '',
    }
  },
  watch: {
    selectedDay: {
      handler(){
        this.setSelectDate(this.selectedDay);
      },
      deep:true,
    },
    selectedWeek: {
      handler(){
        this.setSelectDate(this.selectedWeek);
      },
      deep:true,
    },
    selectedMonth: {
      handler(){
        this.setSelectDate(this.selectedMonth);
      },
      deep:true,
    },
    selectedYear: {
      handler(){
        this.setSelectDate(this.selectedDay);
      },
      deep:true,
    },
    dateType: {
      handler(){
        if(this.type == 'day') this.setSelectDate(this.selectedDay);
        if(this.type == 'week') this.setSelectDate(this.selectedWeek);
        if(this.type == 'month') this.setSelectDate(this.selectedMonth);
        if(this.type == 'year') this.setSelectDate(this.selectedDay);
      },
      deep:true,
    }
  },
  computed: {
    dateType () {
      return this.type.replace(/\b[a-z]/, letter => letter.toUpperCase());
    }
  },
  mounted() {     
    this.today = this.setDateFormat(new Date());
    this.getToday();
  },
  methods: {
    ...mapMutations([
      'setSelectDate'
    ]),
    setDateFormat(date){ //2000
      const year = date.getFullYear();            
      const month = date.getMonth()+1;            
      const day = date.getDate();
      return `${year}-${month >= 10 ? month : '0' + month}-${day >= 10 ? day : '0' + day}`;
    },
    getToday(){
      this.selectedDay = this.today;
      this.selectedWeek = this.getSelectedWeek().week;
      this.selectedMonth = this.getSelectedMonth();
      this.selectedYear = new Date().getFullYear();
    },
    prevDay() {
      let date = this.selectedDay.split('-');
      let selectedDate = new Date(date[0],date[1]-1,date[2]);            
      selectedDate.setDate(selectedDate.getDate() - 1); 

      this.setDayWeekMonth(selectedDate);
    },
    nextDay(){
      let date = this.selectedDay.split('-');
      let selectedDate = new Date(date[0],date[1]-1,date[2]);            
      selectedDate.setDate(selectedDate.getDate() + 1); 

      this.setDayWeekMonth(selectedDate);
    },
    getSelectedWeek() {
      // let week = ['일', '월', '화', '수', '목', '금', '토'];
      let idxOfSelected = this.getDay(this.selectedDay).index;

      let idxOfMonday = 1-idxOfSelected; // 선택한요일 + x = 1 (월요일)
      let idxOfFriday = 5-idxOfSelected; // 선택한요일 + y = 5 (금요일)
      
      let date = this.selectedDay.split('-');
      let dateOfMonday = new Date(date[0], date[1]-1, date[2]*1 + idxOfMonday);
      let dateOfFriday = new Date(date[0], date[1]-1, date[2]*1 + idxOfFriday);
      
      return {
        week : this.setDateFormat(dateOfMonday) + " ~ " + this.setDateFormat(dateOfFriday),
        monday : this.setDateFormat(dateOfMonday)
      };
    },
    prevWeek() {
      let monday = this.getSelectedWeek().monday.split('-');
      let prevMonday = new Date(monday[0], monday[1]-1, monday[2]*1 - 7);

      this.setDayWeekMonth(prevMonday);
    },
    nextWeek() {
      let monday = this.getSelectedWeek().monday.split('-');
      let nextMonday = new Date(monday[0], monday[1]-1, monday[2]*1 + 7);

      this.setDayWeekMonth(nextMonday);
    },
    getSelectedMonth() {
      let date = this.selectedDay.split('-');
      return date[0] + "-" + date[1];
    },
    prevMonth() {
      let date = this.selectedDay.split('-');
      let newDate = new Date(date[0],date[1]-2,1); // 이전달 1일
      let idxOfDay = this.getDay(this.setDateFormat(newDate)).index;

      // 1일이 주말인 경우 다음 첫번째 월요일로 날짜 셋팅
      if(idxOfDay === 0) { // 일요일이면
        newDate = new Date(date[0],date[1]-2,2);
      }
      else if(idxOfDay === 6) { // 토요일이면
        newDate = new Date(date[0],date[1]-2,3);
      }

      this.setDayWeekMonth(newDate);
    },
    nextMonth() {
      let date = this.selectedDay.split('-');
      let newDate = new Date(date[0],date[1],1); // 다음달 1일
      let idxOfDay = this.getDay(this.setDateFormat(newDate)).index;

      // 1일이 주말인 경우 다음 첫번째 월요일로 날짜 셋팅
      if(idxOfDay === 0) { // 일요일이면
          newDate = new Date(date[0],date[1],2);
      }
      else if(idxOfDay === 6) { // 토요일이면
          newDate = new Date(date[0],date[1],3);
      }

      this.setDayWeekMonth(newDate);
    },
    getDay(date) {
      let week = ['일', '월', '화', '수', '목', '금', '토'];
      let idx = new Date(date).getDay();

      return {
        index : idx,
        day : week[idx]
      }
    },
    setDayWeekMonth(date) {
      this.selectedDay = this.setDateFormat(date);
      this.selectedWeek =  this.getSelectedWeek().week;
      this.selectedMonth = this.getSelectedMonth();
      this.selectedYear = date.getFullYear();
    },
    prevYear() {
      let date = this.selectedDay.split('-');
      let selectedDate = new Date(date[0],date[1]-1,date[2]);
      selectedDate.setYear(selectedDate.getFullYear() - 1); 
      this.setDayWeekMonth(selectedDate);
    },
    nextYear(){
      let date = this.selectedDay.split('-');
      let selectedDate = new Date(date[0],date[1]-1,date[2]);
      selectedDate.setYear(selectedDate.getFullYear() + 1); 
      this.setDayWeekMonth(selectedDate);
    }
  },
}
</script>

<style lang="scss" scoped>
.table-status {
  display: inline-flex;
  margin-right: 50px;
  .ow-filter-btn-move {
    &.prev {
      &:after {
        content: "<"
      }
    }
    &.next {
        &:after {
        content: ">"
      }
    }
  }
  .filter-display {
    flex: 0 0 auto;
    padding: 0 20px;
    font-weight: bold;
  }
}
</style>