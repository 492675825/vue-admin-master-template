<template>
  <div class='app-container'>
    <el-menu :default-active="activeIndex" class="el-menu-demo" mode="horizontal" @select="handleSelect">
      <el-menu-item index="1"><router-link to="/daily_data">日数据</router-link></el-menu-item>
      <el-menu-item index="2"><router-link to="/monthly_data">月数据</router-link></el-menu-item>
      <el-menu-item index="3"><router-link to="/yearly_data">年数据</router-link></el-menu-item>
    </el-menu>

    <!--    显示所有数据表格-->
    <el-table :data="gold_data_list"
              border stripe
              highlight-current-row
              height="600">
      <el-table-column type="index" width="50" label="编号"/>
      <el-table-column prop="year_number" label="年份"/>
      <el-table-column prop="totalCount" label="净值"/>
      <el-table-column prop="averageCount" label="年均净值"/>
    </el-table>

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
      this.dataList()
    },

    methods: {
      dataList() {
        axios({
          method: 'get',
          url: `http://localhost:8110/admin/core/gold/yearly_data`,
        }).then(response => {
          this.gold_data_list = response.data.data

        }).catch(response => {
          this.$message.error('error')
        })
      },


    }

  }

</script>
