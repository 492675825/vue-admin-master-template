<template>
  <div class='app-container'>

    <!--    显示更新时间-->
    <span style="float: right;font-size: small;margin-top: 10px;margin-bottom: 10px;">最近更新: {{ refresh_date }}</span>

    <el-button size="mini" style="margin-top: 10px;margin-bottom: 10px" icon="el-icon-download" @click="export_excel">
      导出
    </el-button>


    <el-date-picker
      v-model="select_date_value"
      align="right"
      type="date"
      value-format="yyyy-MM-dd"
      placeholder="选择日期"
      size="mini"
      style="margin-left: 10px"
      :picker-options="pickerOptions">
    </el-date-picker>

    <el-button type="primary" size="mini" style="margin-top: 10px;margin-bottom: 10px;margin-left: 10px"
               icon="el-icon-position" @click="filter_news_by_date">
      前往
    </el-button>


    <!--    显示所有数据表格-->

    <el-table :data="news_data_list"
              border stripe
              :default-sort="{prop: 'versionDate', order: 'descending'}"
              highlight-current-row
              v-if="this.show_total_count">
      <el-table-column type="index" width="50" label="编号"/>
      <el-table-column prop="versionDate" label="发布日期"/>
      <el-table-column prop="title" label="新闻标题"/>
      <el-table-column prop="brief" label="概要"/>
      <el-table-column prop="keyword" label="关键字"/>
      <el-table-column prop="newsType" width="100" label="新闻大类"/>
      <el-table-column prop="refreshDate" width="100" label="系统时间"/>

      <el-table-column prop="url" label="链接">
        <template slot-scope="scope"><a class="click_link" :href="scope.row.url" target="_blank">{{scope.row.url}}</a>
        </template>
      </el-table-column>
    </el-table>


    <!--    显示日期筛选的数据-->

    <el-table :data="news_data_list_by_date"
              border stripe
              :default-sort="{prop: 'versionDate', order: 'descending'}"
              highlight-current-row
              v-if="this.show_select_count">
      <el-table-column type="index" width="50" label="编号"/>
      <el-table-column prop="versionDate" label="发布日期"/>
      <el-table-column prop="title" label="新闻标题"/>
      <el-table-column prop="brief" label="概要"/>
      <el-table-column prop="keyword" label="关键字"/>
      <el-table-column prop="newsType" width="100" label="新闻大类"/>
      <el-table-column prop="refreshDate" width="100" label="系统时间"/>

      <el-table-column prop="url" label="链接">
        <template slot-scope="scope"><a class="click_link" :href="scope.row.url" target="_blank">{{scope.row.url}}</a>
        </template>
      </el-table-column>
    </el-table>

    <!--    分页-->
    <el-pagination
      :current-page='currentPage'
      :page-sizes='[10, 20,30, 40]'
      layout='total, sizes, prev, pager, next, jumper'
      :total='total'
      @size-change='handleSizeChange'
      @current-change='handleCurrentChange'
      v-if="this.show_total_count"
    >
    </el-pagination>
  </div>
</template>


<script>
  import axios from 'axios'

  export default {
    data() {
      return {
        news_data_list: [],
        news_data_list_by_date: [],
        currentPage: 1,
        total: 1,
        pageSize: 10,
        refresh_date: "2022-03-30 10:00:00",
        show_total_count: true,
        show_select_count: false,
        //日期选择器
        select_date_value: "",
        pickerOptions: {
          disabledDate(time) {
            return time.getTime() > Date.now();
          },
          shortcuts: [{
            text: '今天',
            onClick(picker) {
              picker.$emit('pick', new Date());
            }
          }, {
            text: '昨天',
            onClick(picker) {
              const date = new Date();
              date.setTime(date.getTime() - 3600 * 1000 * 24);
              picker.$emit('pick', date);
            }
          }, {
            text: '一周前',
            onClick(picker) {
              const date = new Date();
              date.setTime(date.getTime() - 3600 * 1000 * 24 * 7);
              picker.$emit('pick', date);
            }
          }]
        },
      }
    },
    created() {
      this.getPage()

    },

    methods: {
      getPage() {
        axios({
          method: 'post',
          url: `http://localhost:8110/admin/core/news/getPage`,
          params: {
            currentPage: this.currentPage,
            pageSize: this.pageSize
          }
        }).then(response => {
          this.news_data_list = response.data.data.records
          this.total = response.data.data.total
          this.currentPage = response.data.data.current
          this.refresh_date = response.data.data.records[0].refreshDate

        }).catch(response => {
          this.$message.error('error')
        })
      },
      handleSizeChange(pageSize) {
        this.pageSize = pageSize
        this.getPage()

      },
      handleCurrentChange(currentPage) {
        this.currentPage = currentPage
        this.getPage()
      },

      export_excel() {
        window.location.href = 'http://localhost:8110/admin/core/news/export'
      },
      filter_news_by_date() {

        if (this.select_date_value === "") {
          this.$message.info("请输入日期")
        } else {
          axios({
            url: "http://localhost:8110/admin/core/news/filter_by_date",
            method: "post",
            params: {
              select_date: this.select_date_value
            }
          }).then(res => {
            this.news_data_list_by_date = res.data.data
            this.show_select_count = true
            this.show_total_count = false
          }).catch(res => [
            this.$message.error("error")
          ])
        }

      }
    }

  }

</script>

<!--  点击链接之后文字颜色改变-->
<style>
  .click_link:hover,
  .server-table i.el-tooltip:hover {
    cursor: pointer;
    color: #409EFF;
  }
</style>
