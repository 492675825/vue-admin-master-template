<template>
  <div class='app-container'>
    <el-menu :default-active="activeIndex" class="el-menu-demo" mode="horizontal" @select="handleSelect">
      <el-menu-item index="1"><router-link to="/daily_data"><b>日数据</b></router-link></el-menu-item>
      <el-menu-item index="2"><router-link to="/monthly_data"><b>月数据</b></router-link></el-menu-item>
      <el-menu-item index="3"><router-link to="/yearly_data"><b>年数据</b></router-link></el-menu-item>
      <el-menu-item index="3"><router-link to="/min_max_close"><b>收盘价</b></router-link></el-menu-item>
    </el-menu>

    <!--    显示所有数据表格-->
    <el-table :data="gold_data_list"
              border stripe
              highlight-current-row
              height="600">
      <el-table-column type="index" width="50" label="编号"/>
      <el-table-column prop="yearNumber" label="年份"/>
      <el-table-column prop="minClose" label="最低收盘价"/>
      <el-table-column prop="minCloseVersionDate" label="最低收盘价日期"/>
      <el-table-column prop="minCloseMonth" label="最高收盘所在月"/>
      <el-table-column prop="maxClose" label="最高收盘价"/>
      <el-table-column prop="maxCloseVersionDate" label="最高收盘价日期"/>
      <el-table-column prop="maxCloseMonth" label="最高收盘价所在月"/>
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
          url: `http://localhost:8110/admin/core/gold/minMaxClose`,
        }).then(response => {
          this.gold_data_list = response.data.data

        }).catch(response => {
          this.$message.error('error')
        })
      },


    }

  }

</script>
