<template>
  <div class='app-container'>
    <el-button @click='getDataFromWeb'
               type='primary' size='mini'
               icon='el-icon-refresh'
               style='margin-bottom: 10px'
    >
      更新数据
    </el-button>

    <span style="float: right;font-size: small">最近更新:{{ refresh_date }}</span>

    <!--    显示所有数据表格-->
    <el-table :data="pageList"
              border stripe
              :default-sort="{prop: 'version_date', order: 'descending'}"
              highlight-current-row
    >
      <el-table-column type="index" width="50" label="编号"/>
      <el-table-column prop="version_date" label="日期"/>
      <el-table-column prop="current_value" label="当前值"/>
      <el-table-column prop="predict_value" label="预测值"/>
      <el-table-column prop="previous_value" label="上期值"/>
    </el-table>

    <!--    分页-->
    <el-pagination
      :current-page='currentPage'
      :page-sizes='[10, 20,30, 40]'
      layout='total, sizes, prev, pager, next, jumper'
      :total='total'
      @current-change='handleCurrentChange'
    >
    </el-pagination>

  </div>
</template>
<script>
import axios from 'axios'


export default {
  inject:['reload'],
  data() {
    return {
      list: [],
      refresh_date:"",
      pageList: [],
      currentPage: 1,
      total: 1,
      pageSize: 10,

    }
  },
  created() {
    this.showData()
  },
  methods: {
    getDataFromWeb() {
      this.$message.info("数据更新中..")
      const loading = this.$loading({
        lock: true,
        text: 'Loading',
        spinner: 'el-icon-loading',
        background: 'rgba(0, 0, 0, 0.7)'
      });
      axios({
        method:"get",
        url:"http://127.0.0.1:8000/goldData/get_non_farm/"
      }).then(res=>{
        this.$message.success("success")
        loading.close()
        this.reload()
      }).catch(res=>{
        this.$message.error(res.message)
        loading.close()
      })
    },
    showData(){
      axios({
        method:"post",
        url:"http://127.0.0.1:8000/goldData/show_non_farm/",
        params: {
          currentPage: this.currentPage,
          pageSize: this.pageSize
        }
      }).then(response=>{
        this.pageList = response.data.data.output_page_data
        this.total = response.data.data.total_record_count
        this.refresh_date = response.data.data.refresh_date

      }).catch(response=>{
        this.$message.error(response.methods)
      })
    },

    handleCurrentChange(currentPage) {
      this.currentPage = currentPage
      this.showData()

    },
  }
}

</script>
