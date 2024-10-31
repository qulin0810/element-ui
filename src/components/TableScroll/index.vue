<template>
  <div>
    <div class="content-table">
      <template>
        <div class="scq_header">
           <div class="left-div shadow">
            <div class="left-div1">
              <table>
                <tr>
                  <th
                    class="first-row-style"
                    style="width: 15%; min-width: 60px"
                    v-for="(column, index) in fixedColumns"
                    :key="index + 'fixedColumns' "
                  >
                    <div class="cell"   @click="sortByColumn(column.prop,column.sort)">
                      {{ column.label }}
                      <span v-if="column.sort">
                        <img
                          src="@/static/images/table_triangle_active_down.png"
                          v-if="
                            searchCriteria == column.prop && searchSort == 'DESC'
                          "
                          alt=""
                        />
                        <img
                          src="@/static/images/table_triangle_active_up.png"
                          v-else-if="
                            searchCriteria == column.prop && searchSort == 'ASC'
                          "
                          alt=""
                        />
                        <img
                          src="@/static/images/table_triangle_gray.png"
                          v-else
                          alt=""
                        />
                      </span>
                    </div>
                  </th>
                </tr>
              </table>
            </div>
          </div> 
          <div class="right-div">
            <div ref="firstRowLayer" class="right-div1" @scroll="headerScroll">
              <table class="right-table1">
                <tr>
                  <th
                    v-for="(column, index) in columns"
                    :key="index"
                   
                    class="first-row-style"
                    style="width: 15%; min-width: 65px"
                  >
                    <div class="cell"  @click="sortByColumn(column.prop,column.sort)">
                      {{ column.label }}
                      <span v-if="column.sort">
                        <img
                          src="@/static/images/table_triangle_active_down.png"
                          v-if="
                            searchCriteria == column.prop && searchSort == 'DESC'
                          "
                          alt=""
                        />
                        <img
                          src="@/static/images/table_triangle_active_up.png"
                          v-else-if="
                            searchCriteria == column.prop && searchSort == 'ASC'
                          "
                          alt=""
                        />
                        <img
                          src="@/static/images/table_triangle_gray.png"
                          v-else
                          alt=""
                        />
                      </span>
                    </div>
                  </th>
                </tr>
              </table>
            </div>
          </div>
        </div>
        <div class="body_table">
           <ScrollView ref="scroll" :height="tableScrollHeight">
            <div>
              <div class="body">
                <div ref="firstColLayer" class="left-div2 shadow">
                  <table class="left-table2">
                    <tr
                      v-for="(col, index) in firstCol"
                      :key="index + 'firstCol'"
                    >
                      <td
                        v-for="(column, colIndex) in fixedColumns"
                        :key="colIndex + 'fixedColumns'"
                      >
                          <div
                            slot="reference"
                            class="cell hiddenline2"
                            style="box-sizing: border-box"
                          >
                            {{ col[column.prop] }}
                          </div>
                      </td>
                    </tr>
                  </table>
                </div>
                <div
                  ref="tableContainer"
                  class="right-div2"
                  @scroll="tableScroll($event)"
                >
                  <table class="right-table2">
                    <tr
                      v-for="(item, rowIndex) in tableData1"
                      :key="rowIndex + 'sortedTableData' "
                    >
                      <td v-for="(column, colIndex) in columns" :key="colIndex + 'columns' ">
                        <div class="cell" v-if="column.percent">
                          {{ item[column.prop] }} %
                        </div>
                        <div class="cell" v-else>
                          {{ item[column.prop] }}
                        </div>
                      </td>
                    </tr>
                  </table>
                </div>
              </div>
              <div class="bottomtip" v-show="showTips">
                该表格最多展示50行，请通过筛选条件查询其他数据
              </div>
            </div>
          </ScrollView> 
        </div>
        <div class="none" v-if="tableData1.length == 0">
          <img src="@/static/images/table_none.png" alt="" />
          <p>暂无数据哦～</p>
        </div>
      </template>
    </div>
  </div>
</template>
<script>

import ScrollView from "../TableScroll/scroll-view.vue";
// import { numberToCurrencyNo } from "@/utils";
export default {
  props: {
    // pannelTabIndex: {
    //   type: Number,
    //   default: 0,
    // },
    tableData1: {
      type: Array,
      default: () => [],
    },
    rmqtqList: {
      type: Array,
      default: () => [],
    },
    tableScrollHeight: {
      type: Number,
      default: 0,
    },
    columns: {
      type: Array,
      default: () => [],
      default: () => [
        { label: "年龄", prop: "age" , sort: true , no: 3, percent: true },
        { label: "地址", prop: "address", sort: false , no: 4,percent: false },
        { label: "性别", prop: "sex", sort: true, no: 5,percent: false  },
        { label: "爱好", prop: "hobby", sort: true, no: 6 ,percent: false},
        { label: "爱好1", prop: "hobby1", sort: true, no: 7 ,percent: false},
        { label: "爱好2", prop: "hobby2", sort: true, no: 8 ,percent: false},
        { label: "爱好3", prop: "hobby3", sort: true, no: 9 ,percent: false},
      ],
    },
    // 固定列配置
    fixedColumns: {
      type: Array,
      default: () => [],
      default: () => [
        { label: "ID", prop: "id", sort: false , no: 1, percent: false },
        { label: "姓名", prop: "name", sort: true , no: 2 ,percent: false},
      ],
    },
   // tableData1: {
    //  type: Array,
    //   default: () => [],
    //   default: () => [
    //     { id: 1, name: "John Doe", age: 25, address: "123 Main St" ,sex: '19', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩'},
    //     { id: 2, name: "Jane Smith", age: 30, address: "456 Elm Ave",sex: '29', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
    //     { id: 3, name: "Tom Brown", age: 28, address: "789 Oak Rd",sex: '39',  hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
    //     { id: 1, name: "John Doe", age: 25, address: "123 Main St" ,sex: '19', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩'},
    //     { id: 2, name: "Jane Smith", age: 30, address: "456 Elm Ave",sex: '29', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
    //     { id: 3, name: "Tom Brown", age: 28, address: "789 Oak Rd",sex: '39',  hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
    //     { id: 1, name: "John Doe", age: 25, address: "123 Main St" ,sex: '19', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩'},
    //     { id: 2, name: "Jane Smith", age: 30, address: "456 Elm Ave",sex: '29', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
    //     { id: 3, name: "Tom Brown", age: 28, address: "789 Oak Rd",sex: '39',  hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
    //     { id: 1, name: "John Doe", age: 25, address: "123 Main St" ,sex: '19', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩'},
    //     { id: 2, name: "Jane Smith", age: 30, address: "456 Elm Ave",sex: '29', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
    //     { id: 3, name: "Tom Brown", age: 28, address: "789 Oak Rd",sex: '39',  hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
    //     { id: 1, name: "John Doe", age: 25, address: "123 Main St" ,sex: '19', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩'},
    //     { id: 2, name: "Jane Smith", age: 30, address: "456 Elm Ave",sex: '29', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
    //     { id: 3, name: "Tom Brown", age: 28, address: "789 Oak Rd",sex: '39',  hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
    //     { id: 1, name: "John Doe", age: 25, address: "123 Main St" ,sex: '19', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩'},
    //     { id: 2, name: "Jane Smith", age: 30, address: "456 Elm Ave",sex: '29', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
    //     { id: 3, name: "Tom Brown", age: 28, address: "789 Oak Rd",sex: '39',  hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
    //     { id: 1, name: "John Doe", age: 25, address: "123 Main St" ,sex: '19', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩'},
    //     { id: 2, name: "Jane Smith", age: 30, address: "456 Elm Ave",sex: '29', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
    //     { id: 3, name: "Tom Brown", age: 28, address: "789 Oak Rd",sex: '39',  hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
    //     { id: 1, name: "John Doe", age: 25, address: "123 Main St" ,sex: '19', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩'},
    //     { id: 2, name: "Jane Smith", age: 30, address: "456 Elm Ave",sex: '29', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
    //     { id: 3, name: "Tom Brown", age: 28, address: "789 Oak Rd",sex: '39',  hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
    //     { id: 1, name: "John Doe", age: 25, address: "123 Main St" ,sex: '19', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩'},
    //     { id: 2, name: "Jane Smith", age: 30, address: "456 Elm Ave",sex: '29', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
    //     { id: 3, name: "Tom Brown", age: 28, address: "789 Oak Rd",sex: '39',  hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
    //     { id: 1, name: "John Doe", age: 25, address: "123 Main St" ,sex: '19', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩'},
    //     { id: 2, name: "Jane Smith", age: 30, address: "456 Elm Ave",sex: '29', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
    //     { id: 3, name: "Tom Brown", age: 28, address: "789 Oak Rd",sex: '39',  hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
    //     { id: 1, name: "John Doe", age: 25, address: "123 Main St" ,sex: '19', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩'},
    //     { id: 2, name: "Jane Smith", age: 30, address: "456 Elm Ave",sex: '29', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
    //     { id: 3, name: "Tom Brown", age: 28, address: "789 Oak Rd",sex: '39',  hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },

    //   ],
   // },
    sortArr: {
      type: Array,
      default: () => [],
    },
    leftFixedColumns: {
      type: Array,
      default: () => [],
    },
  },
  components: {
    // Popper,
     ScrollView,
    // Chart,
  },
  data() {
    return {
      isShowTooltip: false,
      searchCriteria: 'age',
      searchSort: "ASC",
      showShadow: false,
      showTableTips: false,
      flag: null,
      timer: null,
      isBottom: false,
      showTips: false,
      tableScrollHeightnew: 0,
      employeeNumber: null,
      // leftFixedColumns: ['id','name']
    };
  },
  watch: {
    tableData1(newval) {
        debugger
      if (newval?.length > 0) {
        if (newval.length == 50) {
          this.showTips = true;
        } else {
          this.showTips = false;
        }
        this.$nextTick(() => {
          setTimeout(() => {
            this.$refs.scroll?.init();
          }, 800);
        });
      }
    },
    tableScrollHeight(newval, oldval) {
      if (oldval !== newval) {
        this.tableScrollHeightnew = newval;
      }
    },
  },
  computed: {
    firstCol() {
      return this.tableData1.map((p) => {
        const newObj = {};
        this.leftFixedColumns.forEach((prop) => {
          newObj[prop] = p[prop];
        });
        return newObj;
      });
    },
    // 变形table
    sortedTableData() {
      const data = [...this.tableData1];
      if (this.searchCriteria) {
        data.sort((a, b) => {
          if (this.searchSort === "ASC") {
            return a[this.searchCriteria] > b[this.searchCriteria] ? 1 : -1;
          } else {
            return a[this.searchCriteria] < b[this.searchCriteria] ? 1 : -1;
          }
        });
      }
      return data;
    },
  },
  mounted() {
    Math.easeInOutQuad = function (t, b, c, d) {
      t /= d / 2;
      if (t < 1) {
        return (c / 2) * t * t + b;
      }
      t--;
      return (-c / 2) * (t * (t - 2) - 1) + b;
    };
  },

  methods: {

    sortByColumn(columnKey,isSort) {
      if(!isSort) return
     if (this.searchCriteria === columnKey) {
         this.searchSort = this.searchSort == "DESC" ? "ASC" : "DESC";
      } else {
        this.searchSort = "DESC";
        this.searchCriteria = columnKey;
      }
      this.$emit(
        "sort",
        this.searchSort,
        columnKey
      );
    },
    smoothScrollTo(element, to, duration) {
      const start = element.scrollLeft;
      const change = to - start;
      const increment = 20;
      let currentTime = 0;

      function animateScroll() {
        currentTime += increment;
        const val = Math.easeInOutQuad(currentTime, start, change, duration);
        element.scrollLeft = val;
        if (currentTime < duration) {
          requestAnimationFrame(animateScroll);
        }
      }
      animateScroll();
    },
    headerScroll() {
      if (this.flag == "body") {
        return;
      }
      this.flag = "header";
      const firstRowLayer = this.$refs.firstRowLayer;

      this.smoothScrollTo(
        this.$refs.tableContainer,
        firstRowLayer.scrollLeft,
        15
      );
      clearTimeout(this.timer);
      this.timer = setTimeout(() => {
        this.flag = null;
      }, 200);
    },
    tableScroll() {
      if (this.flag == "header") {
        return;
      }
      this.flag = "body";
      const tableContainer = this.$refs.tableContainer;
      this.smoothScrollTo(
        this.$refs.firstRowLayer,
        tableContainer.scrollLeft,
        15
      );
      clearTimeout(this.timer);
      this.timer = setTimeout(() => {
        this.flag = null;
      }, 200);
    },
  },
};
</script>
<style scoped lang="scss">

.content-table {
  box-sizing: border-box;
  transform: rotate(0deg);
  -webkit-transform: rotate(0deg);
  overflow: hidden;
}
.scq_header {
  display: flex;
  overflow: hidden;
}
.body_table {
  overflow: hidden;
  .body {
    display: flex;
    touch-action: none;
    overflow: hidden;
  }
  .bottomtip {
    font-size: 10px;
    height: 35px;
    text-align: center;
    line-height: 35px;
  }
}
table {
  border-collapse: collapse;
  margin: 0 auto;
  width: 100%;
  border-spacing: 0;
  font-size: 13px;
}
tr {
  box-sizing: border-box;
  background: #fff;
}
th {
  word-break: break-all;
  word-wrap: break-word;
  height: 36px;
  vertical-align: middle;
  text-align: center;
  box-sizing: border-box;
  background: #d1dbff;
  .cell {
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 12px;
    height: 100%;
    text-align: center;
    img {
      width: 9px;
      height: 14px;
    }
  }
}
td {
  word-break: break-all;
  word-wrap: break-word;
  min-width: 60px;
  height: 35px;
  text-align: center;
  vertical-align: middle;
  box-sizing: border-box;
}

.left-div {
  width: 120px;
  z-index: 1;
}
.shadow {
  box-shadow: 0 -1px 11px 0px rgba(0, 0, 0, 0.12);
}
.left-div1 {
  color: #0733aa;
  background: #d1dbff;
  line-height: 14px;
  width: 120px;
  font-size: 12px;
  overflow: hidden;
  -webkit-overflow-scrolling: touch;
  th {
    text-align: left;
    padding-left: 9px;
    min-width: 0 !important;
    // font-weight: 600;
  }
}
.left-div2 {
  width: 120px;
  z-index: 1;
  -webkit-overflow-scrolling: touch;
  td {
    text-align: center;
    font-size: 13px;
    color: #3e4c87;
    .cell {
      padding-left: 9px;
      font-size: 12px;
      line-height: 17px;
    }
  }
}
.left-table2 {
  .cell {
    width: 60px;
  }
}
.right-div {
  float: left;
  width: calc(100vw - 120px);
  -webkit-overflow-scrolling: touch;
  .last {
    padding-right: 9px;
  }
}
.right-div1 {
  color: #0733aa;
  background: #d1dbff;
  line-height: 14px;
  font-size: 12px;
  width: 100%;
  overflow-x: auto;
  -webkit-overflow-scrolling: touch;
  .first-row-style {
    box-sizing: border-box;
  }
  &::-webkit-scrollbar {
    display: none;
  }
}
.right-div2 {
  width: 100%;
  overflow-y: hidden;
  overflow-x: auto;
  font-size: 13px;
  color: #3e4c87;
  -webkit-overflow-scrolling: touch;
  touch-action: pan-x;
  &::-webkit-scrollbar {
    display: none;
  }
  .cell {
    text-align: right;
    box-sizing: border-box;
    font-family: Din;
    margin: 0 10%;
  }
}

.right-table2 {
  // overflow: hidden;
  -webkit-overflow-scrolling: touch;
}

table tr:nth-child(even) {
  background: #e5edff;
}
.none {
  text-align: center;
  padding-bottom: 40px;
  background: rgba(255, 255, 255, 0.8);
  font-size: 12px;
  color: #465282;
  img {
    width: 194px;
    height: 127px;
    margin-top: 25px;
    margin-bottom: 15px;
  }
  p {
    font-size: 14px;
    text-align: center;
  }
}
</style>