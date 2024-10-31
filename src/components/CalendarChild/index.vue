<template>
  <div class="container">
    <div v-for="(all, a) in allData" :key="a + 'all'">
      <div class="popup-header">
        <div class="popup-header-title">{{ all.yearValue }}年</div>
      </div>
      <div class="header">
        <div
          v-for="item in week"
          :key="item"
          :class="item === '六' || item === '日' ? 'light-color' : ''"
          style="flex: 1"
        >
          {{ item }}
        </div>
      </div>
      <div class="scroll">
        <div style="height: 50px"></div>
        <div
          class="year"
          v-for="(year, index) in all.yearList"
          :key="index + 'year'"
        >
          <div
            class="month"
            v-for="month in year.months"
            :key="month.month + 'month'"
          >
            <div class="month-title">
              <div
                :style="`margin-left: calc(${
                  month.firstDayIndex
                } * (1 / 7 * 100%)); ${
                  month.year != yearValue ? 'width:25%' : ''
                }`"
                class="month-title-text"
              >
                <span v-if="month.year != yearValue">{{ month.year }}年</span
                >{{ month.month }}月
              </div>
            </div>

            <div
              class="week"
              v-for="(week, weekindex) in month.data"
              :key="weekindex + 'week'"
            >
              <div
                v-for="(dayData, dayDataindex) in week.days"
                :key="dayDataindex + 'text'"
                class="day has-boder"
                :class="
                  dayData.isWeekend
                    ? ' weekend-day'
                    : dayData.text
                    ? 'has-boder'
                    : ''
                "
                @click="clickDayEvent(dayData)"
              >
                <div class="date-container">
                  <div class="dayNum">{{ dayData.text }}</div>
                  <div class="dayCn">{{ dayData.IDayCn }}</div>
                </div>

                <div class="event-type-container" v-show="isVisited">
                  <div
                    v-if="
                      dayData.text && (eventData[dayData.dateFormat]&&eventData[dayData.dateFormat].length == 2)
                    "
                  >
                    <div class="icon-bg-area">
                      <img
                        class="icon-submit-visit"
                        src="@/static/images/icon_all_visit.png"
                        alt=""
                      />
                    </div>
                  </div>
                  <div
                    v-for="(item, eventindex) in eventData[dayData.dateFormat]"
                    :key="eventindex + 'event'"
                  >
                    <!-- <div
                      v-if="
                        dayData.text &&
                        eventData[dayData.dateFormat]?.length == 1
                      "
                    >
                      <div class="icon-bg-area">
                        <img
                          v-if="item.eventType === 'isAM'"
                          class="icon-submit-visit"
                          src="@/static/images/icon_half_visit.png"
                          alt=""
                        />
                        <img
                          v-if="item.eventType === 'isPM'"
                          class="icon-submit-visit"
                          style="transform: rotateY(180deg)"
                          src="@/static/images/icon_half_visit.png"
                          alt=""
                        />
                      </div>
                    </div> -->
                  </div>
                </div> 


                <div class="event-type-container" v-show="isCreated">
                  <div
                    v-for="(i, submitIndex) in submitData"
                    :key="submitIndex + 'submitData'"
                  >
                    <div v-if="dayData.text">
                      <div class="icon-bg-area">
                        <img
                          v-if="i == dayData.submitDate"
                          class="icon-submit-visit"
                          src="@/static/images/icon_submit_visit.png"
                          alt=""
                        />
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>


          </div>
        </div>
      </div>
    </div>
    <div class="bottom-content">
      <div class="explain-box">
        <div class="icon-bg-am" v-if="isVisited"></div>
        <div class="icon-bg-pm" v-if="isVisited"></div>
        <img
          v-if="isCreated"
          class="icon-submit-visit"
          src="@/static/images/icon_submit_visit.png"
          alt=""
        />
        <div class="visit-text">
          {{ isVisited ? "拜访天数" : "已提交拜访" }}
        </div>
      </div>
      <div class="explain-text" v-if="isVisited">{{ payVisit }}</div>
      <div class="explain-text" v-else>{{ visitDay }}</div>
    </div>
  </div>
</template>

<script>
import dayjs from "dayjs";
import "@/utils/clendar/zh-cn";
dayjs.locale("zh-cn");

import calendar from "@/utils/clendar/calendar.js";
export default {
  props: {
    visitData: {
      // type: Object,
      // default: () => {},
    },
    creatDateList: {
      type: Array,
      // default: () => {},
    },
    payVisit: {
      type: String,
    },
    visitDay: {
      type: String,
    },
    isCreated1: {
      type: Boolean,
      default: false,
    },
    isVisited1: {
      type: Boolean,
      default: false,
    },
    changeDate: {
      type: String,
    },
  },
  data() {
    return {
      week: ["一", "二", "三", "四", "五", "六", "日"],
      yearValue: dayjs().format("YYYY"),
      yearList: [],
      eventDataList: [],
      eventData: {},
      curYear: new Date().getFullYear(),
      years: {},
      submitData: [],
      allData: [],
    //   isVisited: false,
    };
  },
  watch: {
    visitData(newVal) {
      debugger
      if (newVal) {
        this.getMonthlyCalendarEvent();
      }
    },
    creatDateList(newVal) {
      if (newVal) {
        this.getMonthlySubmitEvent();
      }
    },
    changeDate(newVal){
      if(newVal){
          this.handlebuildCalendarYear();
      }
    }
  },
  created() {
    if (this.yearValue) {
      this.handlebuildCalendarYear();
    }
    this.getMonthlyCalendarEvent();
    this.getMonthlySubmitEvent();
  },
  computed: {
    isVisited() {
      return this.isVisited1;
    },
    isCreated() {
      return this.isCreated1;
    },
  },
  methods: {
    chunk(array, size) {
      let chunks = [];
      for (let i = 0; i < array.length; i += size) {
        chunks.push(array.slice(i, i + size));
      }
      return chunks;
    },
    handlebuildCalendarYear() {
      const _months = [];
      let yearList = [];
      let allData = [];
      const currentDate = dayjs();
      const currentMonth = currentDate.month();
      const currentYear = currentDate.year();
      const monthsArr = [];
      for (let j = 0; j < 6; j++) {
        const monthIndex = (currentMonth - j + 12) % 12;
        let year = currentYear;
        if (currentMonth - j < 0) {
          year--;
        }
        monthsArr.push(`${year}-${monthIndex}`);
      }
      monthsArr.forEach((item) => {
        const [year, month] = item.split("-");
        let monthsData = [];
        monthsData = this.buildMonths(Number(year), Number(month));
        _months.push(monthsData);
        const _year = {
          key: "year" + Number(year),
          year: Number(year),
          months: _months,
        };
        yearList = [_year];
      });
      allData.push({ yearValue: this.yearValue, yearList: yearList });
      this.allData = allData;
    },
    buildMonths(year, monthIndex) {
      const firstDayOfMonth = new Date(year, monthIndex);
      const firstDayOfCalendar = dayjs(firstDayOfMonth).startOf("week");
      const month = new Array(6 * 7).fill("").map((_, i) => {
        const day = dayjs(firstDayOfCalendar).add(i, "day");
        const text = this.getCalendarShowText(day, monthIndex);
        return {
          IDayCn: this.lunarDate(day, text),
          text,
          isWeekend: this.judgeWeekend(day),
          dateFormat: this.getDateFormatString(day),
          key: "day" + year + monthIndex + i,
          submitDate: dayjs(day).format("YYYY-MM-DD"),
        };
      });
      const data = this.chunk(month, 7);
      if (!data[data.length - 1].find((item) => item.text)) data.pop();
      const _weekList = data.map((item, i) => ({
        key: "week" + year + monthIndex + i,
        days: item,
      }));
      return {
        data: _weekList,
        month: monthIndex + 1,
        key: "month_" + year + monthIndex,
        firstDayIndex: this.getFirstDayIndexForMonth(month),
        year: year,
      };
    },
    getCalendarShowText(date, monthIndex) {
      if (dayjs(date).month() != monthIndex) {
        return "";
      } else {
        return date.date();
      }
    },
    judgeWeekend(date) {
      return dayjs(date).day() === 0 || dayjs(date).day() === 6;
    },
    getFirstDayIndexForMonth(monthData) {
      let firstIndex = 0;
      monthData.forEach((item, index) => {
        if (item.text === 1) {
          firstIndex = index;
        }
      });
      return firstIndex;
    },
    lunarDate(date, text) {
      if (!text) {
        return "";
      }
      const Y = dayjs(date).format("YYYY");
      const M = dayjs(date).format("MM");
      const D = dayjs(date).format("DD");
      return calendar.solar2lunar(Y, M, D).IDayCn;
    },
    getDateFormatString(time) {
      const date = new Date(Number(time));
      const year = date.getFullYear();
      const month = String(date.getMonth() + 1);
      const day = String(date.getDate());
      return `${year}-${month}-${day}`;
    },
    getMonthlyCalendarEvent() {
        debugger
      let that = this
      let eventData = {};
      that.visitData?.eventDateList?.forEach((item) => {
        const _ = that.visitData.dataList.find(
          ({ groupValue, calendarItemVoList }) =>
            that.getDateFormatString(groupValue) === item &&
            calendarItemVoList.length > 0
        );
        eventData = {
          [item]:
            _ && _.calendarItemVoList.length
              ? calendar.deDuplicate(
                  _.calendarItemVoList
                    .map((vo, index) => ({
                      eventType:
                        vo.typeLabel === "拜访" &&
                        vo.formattedTime.substring(11, 13) >= 12
                          ? "isPM"
                          : vo.typeLabel === "拜访" &&
                            vo.formattedTime.substring(11, 13) < 12
                          ? "isAM"
                          : "isOther",
                      eventTypeLabel: vo.typeLabel,
                      eventStatus: vo.status,
                      key: item + vo.eventType + vo.typeLabel + index,
                    }))
                    .filter((obj) => obj.eventType !== "isOther"),
                  "eventType"
                )
              : [],
          ...eventData,
        };
      });
      this.eventData = eventData;
    },
    getMonthlySubmitEvent() {
      this.submitData = calendar.deDuplicate(
        this.creatDateList.map((item) => item),
        ""
      );
    },
    clickDayEvent(dayData) {
      const jumpDailyDate = dayjs(
        `${this.yearValue}-${dayData.month}-${dayData.text}`
      ).format("YYYY-MM-DD");
      this.$emit("chooseDate", jumpDailyDate);
    },
  },
};
</script>

<style lang="scss" scoped>
.container {
//   width: 50vw;
  display: flex;
  flex-direction: column;
  z-index: 999;
}
.popup-header-title {
  text-align: center;
  margin-bottom: 5%;
  font-size: 16px;
}
.header {
  height: 20px;
  padding: 5px 15px;
  display: flex;
  align-items: center;
  color: rgba(0, 85, 131, 1);
  font-weight: 600;
  font-size: 12px;
  text-align: center;
}

.light-color {
  color: rgba(0, 85, 131, 0.3);
}

.scroll {
  flex: 1;
  background: #fff;
  padding: 0 15px;
  box-sizing: border-box;
}

.month-title {
  font-size: 16px;
  color: rgba(0, 85, 131, 1);
  font-weight: 600;
  height: 24px;
  display: flex;
  align-items: center;
}

.month-title-text {
  width: calc(1 / 7 * 100%);
  text-align: center;
}

.week {
  display: flex;
}

.day {
  flex: 1;
  height: 80px;
  color: rgba(0, 85, 131, 1);
  text-align: center;
}

.dayCn {
  font-size: 10px;
}

.dayNum {
  font-size: 16px;
}

.has-boder {
  border-top: 0.5px solid rgba(0, 85, 131, 0.15);
}

.weekend-day {
  color: rgba(0, 85, 131, 0.3);
}

.date-container {
  height: 60px;
  padding-top: 8px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  box-sizing: border-box;
}

.event-type-container {
  display: flex;
  height: 20px;
  max-width: 50px;
  justify-content: center;
}

.event-type {
  width: 10px;
  height: 10px;
  border: 1px solid rgba(255, 255, 255, 1);
  border-radius: 50%;
  box-sizing: border-box;
}

.bg1 {
  background-color: rgba(75, 183, 53, 1);
}

.bg2 {
  background-color: rgba(0, 85, 131, 1);
}

.bg3 {
  background-color: rgba(255, 105, 105, 1);
}

.bg4 {
  background-color: rgba(255, 166, 105, 1);
}

.bg5 {
  background-color: rgba(52, 170, 247, 1);
}

.bg6 {
  background-color: rgba(177, 105, 255, 1);
}

::-webkit-scrollbar {
  display: none;
  width: 0;
  height: 0;
  color: transparent;
}

.icon-bg-area {
  display: flex;
}

.icon-bg-am {
	width: 6px;
	height: 12px;
	background: rgba(52, 170, 247, 1);
	border-radius: 6px 0 0 6px;
	margin-right: 1px;
}

.icon-bg-pm {
	width: 6px;
	height: 12px;
	background: rgba(52, 170, 247, 1);
	border-radius: 0 6px 6px 0;
}

.icon-bg-am-gray {
  width: 3px;
  height: 6px;
  background: rgba(52, 170, 247, 0.15);
  border-radius: 3px 0 0 3px;
  margin-right: 0.5px;
}

.icon-bg-pm-gray {
  width: 3px;
  height: 6px;
  background: rgba(52, 170, 247, 0.15);
  border-radius: 0 3px 3px 0;
}

.bottom-content {
  height: 150px;
  border-top: 0.5px solid rgba(0, 85, 131, 0.15);
  /* display: flex; */
  align-items: center;
  justify-content: center;
}

.visit-text {
  font-size: 14px;
  line-height: 14px;
  color: rgba(0, 85, 131, 0.6);
  margin-left: 6px;
}

.icon-submit-visit {
  width: 22px;
  height: 14px;
}

.explain-box {
  width: 100%;
  display: flex;
  height: 50px;
  text-align: center;
  justify-content: center;
  align-items: center;
}

.explain-text {
  font-size: 12px;
  background-color: rgba(234, 239, 243, 0.4);
  padding: 10px 10px;
  color: rgba(0, 85, 131, 0.473);
  border-radius: 10px;
  margin: 0 20px;
}
</style>
