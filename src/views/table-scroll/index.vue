<template>
  <div class="app-container">
    <div ref="myElement" class="myElement" style="height:50px"></div>
    <div class="table-box">
         <TableScroll
        :tableData1="tableData"
        :tableScrollHeight="distanceToPageBottom"
        :showNone="showNone"
      />
    </div>

  </div>
</template>

<script>
import { getList } from '@/api/table'
import TableScroll from '@/components/TableScroll/index.vue'
// import tablescroll from "../../../mock/data/tablescroll.json";
export default {
  filters: {
    statusFilter(status) {
      const statusMap = {
        published: 'success',
        draft: 'gray',
        deleted: 'danger'
      }
      return statusMap[status]
    }
  },
  data() {
    return {
      list: null,
      listLoading: true,
      tableData: [],
      distanceToPageBottom: 0,
      showNone: false,
    }
  },
  components:{
    TableScroll
  },
  created() {
    this.fetchData()
  },
  mounted(){
    this.tableData.push(
         { id: 1, name: "John Doe", age: 25, address: "123 Main St" ,sex: '19', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩'},
        { id: 2, name: "Jane Smith", age: 30, address: "456 Elm Ave",sex: '29', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
        { id: 3, name: "Tom Brown", age: 28, address: "789 Oak Rd",sex: '39',  hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
        { id: 1, name: "John Doe", age: 25, address: "123 Main St" ,sex: '19', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩'},
        { id: 2, name: "Jane Smith", age: 30, address: "456 Elm Ave",sex: '29', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
        { id: 3, name: "Tom Brown", age: 28, address: "789 Oak Rd",sex: '39',  hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
        { id: 1, name: "John Doe", age: 25, address: "123 Main St" ,sex: '19', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩'},
        { id: 2, name: "Jane Smith", age: 30, address: "456 Elm Ave",sex: '29', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
        { id: 3, name: "Tom Brown", age: 28, address: "789 Oak Rd",sex: '39',  hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
        { id: 1, name: "John Doe", age: 25, address: "123 Main St" ,sex: '19', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩'},
        { id: 2, name: "Jane Smith", age: 30, address: "456 Elm Ave",sex: '29', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
        { id: 3, name: "Tom Brown", age: 28, address: "789 Oak Rd",sex: '39',  hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
        { id: 1, name: "John Doe", age: 25, address: "123 Main St" ,sex: '19', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩'},
        { id: 2, name: "Jane Smith", age: 30, address: "456 Elm Ave",sex: '29', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
        { id: 3, name: "Tom Brown", age: 28, address: "789 Oak Rd",sex: '39',  hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
        { id: 1, name: "John Doe", age: 25, address: "123 Main St" ,sex: '19', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩'},
        { id: 2, name: "Jane Smith", age: 30, address: "456 Elm Ave",sex: '29', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
        { id: 3, name: "Tom Brown", age: 28, address: "789 Oak Rd",sex: '39',  hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
        { id: 1, name: "John Doe", age: 25, address: "123 Main St" ,sex: '19', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩'},
        { id: 2, name: "Jane Smith", age: 30, address: "456 Elm Ave",sex: '29', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
        { id: 3, name: "Tom Brown", age: 28, address: "789 Oak Rd",sex: '39',  hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
        { id: 1, name: "John Doe", age: 25, address: "123 Main St" ,sex: '19', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩'},
        { id: 2, name: "Jane Smith", age: 30, address: "456 Elm Ave",sex: '29', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
        { id: 3, name: "Tom Brown", age: 28, address: "789 Oak Rd",sex: '39',  hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
        { id: 1, name: "John Doe", age: 25, address: "123 Main St" ,sex: '19', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩'},
        { id: 2, name: "Jane Smith", age: 30, address: "456 Elm Ave",sex: '29', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
        { id: 3, name: "Tom Brown", age: 28, address: "789 Oak Rd",sex: '39',  hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
        { id: 1, name: "John Doe", age: 25, address: "123 Main St" ,sex: '19', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩'},
        { id: 2, name: "Jane Smith", age: 30, address: "456 Elm Ave",sex: '29', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
        { id: 3, name: "Tom Brown", age: 28, address: "789 Oak Rd",sex: '39',  hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
        { id: 1, name: "John Doe", age: 25, address: "123 Main St" ,sex: '19', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩'},
        { id: 2, name: "Jane Smith", age: 30, address: "456 Elm Ave",sex: '29', hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
        { id: 3, name: "Tom Brown", age: 28, address: "789 Oak Rd",sex: '39',  hobby: '玩', hobby1: '玩',hobby2: '玩',hobby3: '玩' },
    )
    this.getElementBottomDistance();
  },
  methods: {
    fetchData() {
      this.listLoading = true
      getList().then(response => {
        this.list = response.data.items
        this.listLoading = false
      })
    },
    getElementBottomDistance() {
      debugger
      const element = this.$refs.myElement;
      if (element) {
        const rect = element.getBoundingClientRect();
        const scrollTop =
          window.pageYOffset ||
          document.documentElement.scrollTop ||
          document.body.scrollTop;
        const clientHeight =
          window.innerHeight || document.documentElement.clientHeight;
        const elementBottom = rect.bottom + scrollTop;
        const distanceToPageBottom = clientHeight - (elementBottom - scrollTop);
        this.distanceToPageBottom = distanceToPageBottom;
      }
    },
  }
}
</script>
