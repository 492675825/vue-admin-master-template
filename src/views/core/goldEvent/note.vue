<template>
  <div class='app-container'>
    <!--添加-->
    <el-dialog title="添加" :visible.sync="dialogAddVisible">
      <el-form>
        <el-input
          type="textarea"
          maxlength="1000"
          show-word-limit
          :rows="10"
          placeholder="请输入内容"
          v-model="text"
        >
        </el-input>
      </el-form>
      <el-button size="mini" type="primary" style="margin-top: 10px;" @click="addConfirm">
        确定
      </el-button>
      <el-button size="mini" type="primary" style="margin-top: 10px" @click="addRest">
        重置
      </el-button>
    </el-dialog>
    <!--    查看-->
    <el-dialog title="查看" :visible.sync="dialogViewVisible">
      <el-form>
        <el-input
          type="textarea"
          maxlength="1000"
          show-word-limit
          :rows="10"
          placeholder="请输入内容"
          v-model="text"
        >
        </el-input>
      </el-form>
    </el-dialog>
    <!--修改-->
    <el-dialog title="修改" :visible.sync="dialogUpdateVisible">
      <el-form>
        <el-input
          type="textarea"
          maxlength="1000"
          show-word-limit
          :rows="10"
          placeholder="请输入内容"
          v-model="text"
        >
        </el-input>
      </el-form>
      <el-button size="mini" type="primary" style="margin-top: 10px;" @click="confirm">
        确定
      </el-button>
      <el-button size="mini" type="primary" style="margin-top: 10px" @click="reset">
        重置
      </el-button>
    </el-dialog>

    <!--添加/查看/修改按钮-->
    <el-button type="primary" size="mini" icon="el-icon-circle-plus" style="margin-bottom: 10px"
               @click="add">
      添加
    </el-button>
    <el-table :data="dataList" border stripe>
      <el-table-column type="index" width="50" label="编号"/>
      <el-table-column prop="id" label="内部编号" align="center" v-if="show"/>
      <el-table-column prop="version_date" width="100" label="日期" align="center"/>
      <el-table-column prop="context" label="随笔" align="center" :show-overflow-tooltip='true'/>
      <el-table-column label="操作" width="280" align="center">
        <template slot-scope="scope">
          <el-button type="primary" size="mini"
                     icon="el-icon-view"
                     @click="dialogViewVisible=true;getInfoById(scope.row)">
            查看
          </el-button>

          <el-button type="warning" size="mini"
                     icon="el-icon-edit"
                     @click="dialogUpdateVisible=true;getInfoById(scope.row)">
            修改
          </el-button>
          <el-button type="danger" size="mini" icon="el-icon-delete" @click="delete_data(scope.row)">
            删除
          </el-button>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>


<script>
import axios from 'axios'

export default {
  inject: ['reload'],
  data() {
    return {
      dataList: [],
      show: false,
      visible: false,
      dialogViewVisible: false,
      dialogUpdateVisible: false,
      dialogAddVisible: false,
      id_number: 1,
      text: "",

    }
  },
  created() {
    this.noteList()
  },
  methods: {
    //显示数据库中所有的数据
    noteList() {
      axios({
        method: "get",
        url: 'http://127.0.0.1:8000/goldData/note/'
      }).then(res => {
        this.dataList = res.data.data
      }).catch(res => {
        this.$message.error("error")
      })
    },
    //添加
    add() {
      this.dialogAddVisible = true
    },

    //删除
    delete_data(row) {
      this.$confirm('此操作将永久删除改记录，是否继续', '提示',
        {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
        axios({
          method: "post",
          url: `http://127.0.0.1:8000/goldData/deleteById/`,
          params: {
            id_number: row.id
          }
        }).then(res => {
          this.reload()
          this.$message.success("success")
        }).catch(res => {
          this.$message.error(res.message)
        })
      })
    },

    //对话框查看
    getInfoById(row) {
      axios({
        method: "get",
        url: `http://127.0.0.1:8000/goldData/getInfoById/${row.id}`
      }).then(res => {
        this.text = res.data.data
        this.id_number = row.id

      }).catch(res => {
        this.$message.error("error")
      })
    },

    //对话框修改的确认按钮
    confirm() {
      if(this.text === ""){
        this.$message.error("内容不能为空")
      }else {
        axios({
          method: 'post',
          url: 'http://127.0.0.1:8000/goldData/updateById/',
          params: {
            text: this.text,
            id_number: this.id_number
          }
        }).then(res => {
          this.dialogUpdateVisible = false
          this.$message.success("success")
          this.reload()

        }).catch(res => {
          this.$message.error(res.message)
        })
      }
    },

    //对话框修改的重置按钮
    reset() {
      axios({
        method: "get",
        url: `http://127.0.0.1:8000/goldData/getInfoById/${this.id_number}`
      }).then(res => {
        this.text = res.data.data
      }).catch(res => {
        this.$message.error("error")
      })
    },

    //对话框添加的确认按钮
    addConfirm() {
      if(this.text === ""){
        this.$message.error("内容不能为空")
      }else {
        axios({
          method: 'post',
          url: 'http://127.0.0.1:8000/goldData/addNote/',
          params: {
            text: this.text
          }
        }).then(res => {
          this.$message.success("success")
          this.reload()
        }).catch(res => {
          this.$message.error(res.message)
        })
      }
    },

    //对话框添加的重置按钮
    addRest() {
      this.text = ""
    }
  }
}
</script>
