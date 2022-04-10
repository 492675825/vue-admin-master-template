<template>
  <div class='app-container'>
    <el-menu :default-active="activeIndex" class="el-menu-demo" mode="horizontal" @select="handleSelect">
      <el-menu-item index="1">
        <router-link to="/daily_data">日数据</router-link>
      </el-menu-item>
      <el-menu-item index="2">
        <router-link to="/monthly_data">月数据</router-link>
      </el-menu-item>
      <el-menu-item index="3">
        <router-link to="/yearly_data">年数据</router-link>
      </el-menu-item>
    </el-menu>
    <!--    显示更新时间-->
    <span style="float: right;font-size: small;margin-top: 10px;margin-bottom: 10px;">最近更新: {{ refresh_date }}</span>

    <el-button size="mini" style="margin-top: 10px;margin-bottom: 10px" icon="el-icon-download" @click="export_excel">导出</el-button>

    <!--    显示所有数据表格-->
    <el-table :data="gold_data_list"
              border stripe
              :default-sort="{prop: 'version_date', order: 'descending'}"
              highlight-current-row>
      <el-table-column type="index" width="50" label="编号"/>
      <el-table-column prop="versionDate" label="交易日期"/>
      <el-table-column prop="item" label="产品类别"/>
      <el-table-column prop="open" label="开盘价"/>
      <el-table-column prop="high" label="最高价"/>
      <el-table-column prop="low" label="最低价"/>
      <el-table-column prop="close" label="收盘价"/>
      <el-table-column prop="upOrDown" label="涨幅"/>
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
          url: `http://localhost:8110/admin/core/gold/getPage`,
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
      }
      , push_to_month() {
        this.$router.push({path: "GoldMonthlyData"})
      },
      export_excel(){
        window.location.href = 'http://localhost:8110/admin/core/gold/export'
      }

    }

  }

</script>
