<template>
  <div class='app-container'>

    <!--    显示更新时间-->
    <span style="float: right;font-size: small;margin-top: 10px;margin-bottom: 10px;">最近更新: {{ refresh_date }}</span>

    <el-button size="mini" style="margin-top: 10px;margin-bottom: 10px" icon="el-icon-download" @click="export_excel">导出</el-button>

    <!--    显示所有数据表格-->
    <el-table :data="gold_data_list"
              border stripe
              :default-sort="{prop: 'version_date', order: 'descending'}"
              highlight-current-row>
      <el-table-column type="index" width="50" label="编号"/>
      <el-table-column prop="versionDate" label="公布日期"/>
      <el-table-column prop="currentValue" label="今值(万人)"/>
      <el-table-column prop="predictValue" label="预测值(万人)"/>
      <el-table-column prop="previousValue" label="前值(万人)"/>
    </el-table>

    <!--    分页-->
    <el-pagination
      :current-page='currentPage'
      :page-sizes='[10, 20,30, 40]'
      layout='total, sizes, prev, pager, next, jumper'
      :total='total'
      @size-change='handleSizeChange'
      @current-change='handleCurrentChange'
    >
    </el-pagination>
  </div>
</template>


<script>
  import axios from 'axios'

  export default {
    data() {
      return {
        gold_data_list: [],
        currentPage: 1,
        total: 1,
        pageSize: 10,
        refresh_date: "2022-03-30 10:00:00"
      }
    },
    created() {
      this.getPage()
    },

    methods: {
      getPage() {
        axios({
          method: 'post',
          url: `http://localhost:8110/admin/core/nonfarm/data`,
          params: {
            currentPage: this.currentPage,
            pageSize: this.pageSize
          }
        }).then(response => {
          this.gold_data_list = response.data.data.records
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

      export_excel(){
        window.location.href = 'http://localhost:8110/admin/core/nonfarm/export'
      }

    }

  }

</script>
