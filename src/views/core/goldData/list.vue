<template>
  <div class='app-container'>
    <el-button @click='getDataFromWeb'
               type='primary' size='mini'
               icon='el-icon-refresh'
               style='margin-bottom: 10px'

    >
      更新数据库
    </el-button>
    <!--    excel文件导出-->
    <el-button
      type='primary' size='mini'
      icon='el-icon-download'
      style='margin-bottom: 10px'

    >
      <a href="http://127.0.0.1:8000/goldData/exportGold/"
         @click="export_gold_data">
        导出表格
      </a>
    </el-button>

    <!--    日期选择-->
    <el-date-picker
      @change="getPage"
      v-model="time_number"
      type="date"
      placeholder="日期筛选"
      size="mini"
      style="margin-left: 10px"
      value-format="yyyy-MM-dd"

    >
    </el-date-picker>

    <!--    显示更新时间-->
    <span style="float: right;font-size: small">最近更新:{{ refresh_date }}</span>

    <!--    显示所有数据表格-->
    <el-table :data="pageList"
              border stripe
              :default-sort="{prop: 'version_date', order: 'descending'}"
              highlight-current-row
              v-if="tableShow">
      <el-table-column type="index" width="50" label="编号"/>
      <el-table-column prop="version_date" label="交易日期"/>
      <el-table-column prop="item" label="产品类别"/>
      <el-table-column prop="open" label="开盘价"/>
      <el-table-column prop="high" label="最高价"/>
      <el-table-column prop="low" label="最低价"/>
      <el-table-column prop="close" label="收盘价"/>
      <el-table-column prop="up_or_down" label="涨幅"/>
      <el-table-column prop="mapping" label="标记"/>
    </el-table>

    <!--    指定日期表格-->
    <el-table :data="selectByDateList"
              border stripe
              :default-sort="{prop: 'version_date', order: 'descending'}"
              highlight-current-row
              v-if="selectDataTableShow">
      <el-table-column type="index" width="50" label="编号"/>
      <el-table-column prop="version_date" label="交易日期"/>
      <el-table-column prop="item" label="产品类别"/>
      <el-table-column prop="open" label="开盘价"/>
      <el-table-column prop="high" label="最高价"/>
      <el-table-column prop="low" label="最低价"/>
      <el-table-column prop="close" label="收盘价"/>
      <el-table-column prop="up_or_down" label="涨幅"/>
      <el-table-column prop="mapping" label="标记"/>
    </el-table>

    <!--    分页-->
    <el-pagination
      :current-page='currentPage'
      :page-sizes='[10, 20,30, 40]'
      layout='total, sizes, prev, pager, next, jumper'
      :total='total'
      @size-change='handleSizeChange'
      @current-change='handleCurrentChange'
      v-if="tableShow"
    >
    </el-pagination>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      all_gold_data: [],
      pageList: [],
      selectByDateList: [],
      currentPage: 1,
      total: 1,
      pageSize: 10,
      refresh_date: "",
      time_number: "",
      tableShow: false,
      selectDataTableShow: false,

    }
  },
  created() {
    // this.getAllDataFromMysql();
    this.getPage()

  },

  methods: {
    //数据库更新功能
    getDataFromWeb() {
      //发送get请求
      this.$message.info("数据更新中..")
      const loading = this.$loading({
        lock: true,
        text: 'Loading',
        spinner: 'el-icon-loading',
        background: 'rgba(0, 0, 0, 0.7)'
      });

      axios({
        method: "get",
        url: 'http://127.0.0.1:8000/goldData/getDataFromWeb/',
      }).then(res => {
        this.$message.success("success..")
        loading.close();
      }).catch(res => {
        this.$message.error(res.message)
        loading.close();
      })
    },
    //数据库数据获取功能
    getAllDataFromMysql() {
      if (this.time_number === "") {
        this.tableShow = true
        axios({
          method: "get",
          url: 'http://127.0.0.1:8000/goldData/allData/'
        }).then(res => {
          this.all_gold_data = res.data.data
        }).catch(res => {
          this.$message(res.message);
        })
      } else {
        this.tableShow = false
      }

    },

    handleSizeChange(pageSize) {
      this.pageSize = pageSize
      this.getPage()
    },
    handleCurrentChange(currentPage) {
      this.currentPage = currentPage
      this.getPage()

    },

    //分页
    getPage(num) {
      if (this.time_number === null || this.time_number === "") {  //如果没有选择日期或日期为空的时候显示所有数据
        this.tableShow = true
        this.selectDataTableShow = false
        axios({
          method: 'post',
          url: `http://127.0.0.1:8000/goldData/getPage/`,
          params: {
            currentPage: this.currentPage,
            pageSize: this.pageSize
          }
        }).then(response => {
          this.pageList = response.data.data.output_page_data
          this.total = response.data.data.total_record_count
          this.refresh_date = response.data.data.refresh_date
        }).catch(response => {
          this.$message.error('error')
        })
      } else {  //  如果有选择指定的日期时显示对应日期的数据
        this.selectDataTableShow = true
        this.tableShow = false
        axios({
          method: "post",
          url: "http://127.0.0.1:8000/goldData/selectByDate/",
          params: {
            version_date: this.time_number
          }
        }).then(res => {
          this.selectByDateList = res.data.data
          if (this.selectByDateList.length === 0) {  //如果当天没有数据，返回提示
            this.$message.info("无当天数据")
          } else {
          }
        }).catch(res => {
          this.$message.error(res.message)
        })
      }
    },
    //  导出excel表格
    export_gold_data() {
      axios({
        method: "get",
        url: `http://127.0.0.1:8000/goldData/exportGold/`
      }).then(res => {
        this.$message.success("导出成功！");
      }).catch(res => {
        this.$message.error(res.message);
      })
    },


  }

}

</script>
