<template>
  <div class="dashboard-container">
    <div class="dashboard-text">name: {{ name }}</div>
    <el-button @click="dialogVisible = true">点击打开日历</el-button>
    <el-dialog
      title="提示"
      :visible.sync="dialogVisible"
      width="30%"
    >
      <span>这是一段信息</span>
      <!-- <Calendar></Calendar> 这个名称会报错，页面重复-->
      <CalendarChild :visitData="visitData" :creatDateList="creatDateList"  :payVisit="payVisit" :visitDay="visitDay" :isCreated1="isCreated" :isVisited1="isVisited" :changeDate="changeDate" ></CalendarChild>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="dialogVisible = false"
          >确 定</el-button
        >
      </span>
    </el-dialog>
  </div>
</template>

<script>
import { mapGetters } from "vuex";
import axios from "axios";
import calendarList from "../../../../mock/data/calendar.json";
import CalendarChild from "@/components/CalendarChild";
import dayjs from "dayjs";
export default {
  name: "Calendar",
  data() {
    return {
      dialogVisible: false,
      visitData: "",
      creatDateList: "",
      payVisit: "",
      visitDay: "",
      isVisited: true, //页下有解释
      isCreated: false,
      changeDate: "",
      calendarData: calendarList.data,
      isCreated1: "",
      isVisited1: ""
    };
  },
  created() {
   // this.fetchData();
    this.fetchData1();
  },
  components: {
    // Calendar,
    CalendarChild,
  },
  computed: {
    ...mapGetters(["name"]),
  },
  methods: {
    handleClose() {
      console.log("close");
    },
    fetchData() {
      const jsonFilePath = "http://localhost:9528/data/calendar.json";
      let that = this 
      axios
        .get(jsonFilePath)
        .then((response) => {
          const creatDateList = [];
          response.data.data.dataList.map((item) => {
            item.calendarItemVoList.filter((date) => {
              if (date.typeLabel == "拜访") {
                creatDateList.push(
                  dayjs(date.createdTimestamp).format("YYYY-MM-DD")
                );
              }
            });
          });
          that.visitData = response.data?.data || [];
          that.creatDateList = creatDateList;
        })
        .catch((error) => {
          console.error(error);
        })
        .finally((error) => {
          console.error(error);
        });
    },
    fetchData1(){
      let that = this
      const creatDateList = [];
      that.calendarData.dataList.map((item) => {
        item.calendarItemVoList.filter((date) => {
          if (date.typeLabel == "拜访") {
            creatDateList.push(
              dayjs(date.createdTimestamp).format("YYYY-MM-DD")
            );
          }
        });
      });
      that.visitData = that.calendarData || [];
      console.log(that.visitData)
      that.creatDateList = creatDateList;
    },
  },
};
</script>

<style lang="scss" scoped>
.dashboard {
  &-container {
    margin: 30px;
  }
  &-text {
    font-size: 30px;
    line-height: 46px;
  }
}
</style>
 <!--需要备注的方法
 引用数据的方法有两个
 1.在.json文件定义,import直接引用
 2.使用axios请求获取数据

 变量定义
    isVisited: false, 拜访天数数据 半圆
    isCreated: true,  已提交拜访数 ♥
    显示哪个把哪个变成true

 -->