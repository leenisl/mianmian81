<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-table :data="tableData" border fit style="width: 100%">
        <el-table-column type="index" label="序号" width="50px"></el-table-column>
        <el-table-column prop="addTime" label="组题时间" align="center"></el-table-column>
        <el-table-column prop="userName" label="用户名" align="center"></el-table-column>
        <el-table-column label="试题ID" align="center">
          <template slot-scope="scope">
            <span @click="detailItem()">{{scope.row.questionIDs|subStr}}</span>
          </template>
        </el-table-column>
        <el-table-column prop="progressOfAnswer" label="答题进度" align="center"></el-table-column>
        <el-table-column prop="accuracyRate" label="正确率" align="center"></el-table-column>
        <el-table-column prop="totalSeconds" label="答题总耗时(秒)" align="center"></el-table-column>
        <el-table-column prop="questionType" label="组题类型/详情" align="center"></el-table-column>
        <el-table-column align="center " label="操作">
          <template slot-scope="scope">
            <el-button type="text" @click="delRandoms(scope.row)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
      <!-- 分页效果 -->
      <el-row type="flex" justify="end" style="margin-top:20px">
        <el-pagination
          @current-change="CurrentChange"
          :current-page="page.currentPage"
          :page-size="page.pageSize"
          layout="total, prev, pager, next, jumper"
          :total="page.total"
        ></el-pagination>
      </el-row>
    </div>
  </div>
</template>

<script>
import { randoms, removeRandoms } from '@/api/hmmm/questions'
export default {
  name: 'QuestionsRandoms',
  data() {
    return {
      tableData: [],
      page: {
        currentPage: 1,
        pageSize: 10
      }
    }
  },
  methods: {
    // 点击删除列表数据
    delRandoms(info) {
      this.$confirm('此操作将会删除该条数据' + ',是否继续?', '提示', {type: 'warning'}).then(
        async () => {
          await removeRandoms(info)
            .then(response => {            
              // this.tableData.splice(id, 1)
              this.$message.success('已成功删除!')
              this.getrandoms()
            })
            .catch(response => {
              this.$message.error('删除失败!')
            })
        }).catch(() => {
           this.$message.info('已取消操作!')
        })
    },
    // 点击页码切换并重新更新数据
    CurrentChange(newPage) {
      this.page.currentPage = newPage
      this.getrandoms()
    },
    // 获取组题列表数据
    async getrandoms() {
      let getdata = await randoms(this.page)
      //  将获取数据给tableData
      // console.log(getdata)
      this.tableData = getdata.data.items
      // 获取总条数
      this.page.total = getdata.data.counts
    }
  },
  // 过滤题目ID,将大数字简短
  filters: {
    subStr: function(value) {
      return value.map((item, index) => { 
       return item.slice(-4).toString()
        }).join(',')       
    }   
  },
  created() {
    this.getrandoms()
  }
}
</script>

<style scoped>
</style>
