<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-card style="height:100%;width:100%">
        <div class="addBtn">
          <el-button style="background-color:#ccc" @click="addSubjects">新增学科</el-button>
          <el-dialog
            :title="addOrModification?'新增学科':'修改学科'"
            :visible.sync="dialogVisible"
            width="30%"
            :before-close="handleClose"
          >
            <div>
              <span>学科名称</span>
              <el-input
                placeholder="请输入学科名称"
                style="width:200px;margin-left:10px"
                v-model="addSubject"
              ></el-input>
            </div>
            <div>
              <span>是否显示</span>
              <el-switch
                v-model="value"
                active-color="#13ce66"
                inactive-color="#ff4949"
                style="margin:10px;"
              ></el-switch>
            </div>
            <span slot="footer" class="dialog-footer">
              <el-button @click="dialogVisible = false">取 消</el-button>
              <el-button type="primary" @click="addOrEdit">确 定</el-button>
            </span>
          </el-dialog>
        </div>
        <div class="search">
          <span>学科名称</span>
          <el-input placeholder="请输入" class="input" v-model="subjectNames"></el-input>
          <span class="operationBtn">
            <el-button @click="claer">清除</el-button>
            <el-button type="primary" @click="search">搜索</el-button>
          </span>
        </div>
        <el-table :data="subjeictList" style="width: 100%">
          <el-table-column prop="id" label="序号"></el-table-column>
          <el-table-column prop="subjectName" label="学科名称"></el-table-column>
          <el-table-column prop="creator" label="创建者"></el-table-column>
          <el-table-column prop="addDate" label="创建日期" width="200">
            <span slot-scope="obj">{{obj.row.addDate | parseTimeByString}}</span>
          </el-table-column>
          <el-table-column prop="isFrontDisplay" label="前台是否显示" :formatter="formatterShow"></el-table-column>
          <el-table-column prop="twoLevelDirectory" label="二级目录"></el-table-column>
          <el-table-column prop="tags" label="标签"></el-table-column>
          <el-table-column prop="totals" label="题目数量"></el-table-column>
          <el-table-column label="操作" width="340">
            <div slot-scope="obj" style="width:340px;font-size:12px;color:#108ee9">
              <span style="cursor:pointer" @click="goDirectorys(obj.row.id)">学科分类</span>
              <span style="cursor:pointer" @click="goTags(obj.row.id)">学科标签</span>
              <span style="cursor:pointer" @click="edit(obj.row)">修改</span>
              <span style="cursor:pointer" @click="del(obj.row)">删除</span>
            </div>
          </el-table-column>
        </el-table>
        <div class="block">
          <el-pagination
            :page-sizes="[ 10, 20, 30]"
            :page-size="pages.pageSize"
            layout="total, sizes, prev, pager, next, jumper"
            :total="pages.counts"
            @current-change="changPage"
            @size-change="changeSize"
          ></el-pagination>
        </div>
      </el-card>
    </div>
  </div>
</template>

<script>
import { list, remove, add, update } from '@/api/hmmm/subjects' // 获取学科列表信息
export default {
  name: 'SubjectsList',
  data() {
    return {
      addOrModification: true, // 区分添加或者修改
      id: null, // 当前的id
      subjectNames: null, // 搜索学科输入框的值
      addSubject: null, // 新增学科输入框的值
      value: true, // 学科是否显示的值
      dialogVisible: false, // 对话框
      subjeictList: [], // 获取数据的列表
      pages: {
        page: 1, // 当前页数
        pagesize: 10, // 每页数量
        counts: 100 // 总页数
      },
      transience: null // 中间存的一个值,用于搜索时分页改变的时候加载页面的值
    }
  },
  methods: {
    // 修改
    edit(edit) {
      // this.titles = false
      this.addOrModification = false
      this.dialogVisible = true
      this.addSubject = edit.subjectName
      this.id = edit.id
    },
    // 新增学科
    addSubjects() {
      // this.titles = true
      this.dialogVisible = true
      this.addOrModification = true
    },
    // 点击确定按钮
    async addOrEdit() {
      let data = {
        subjectName: this.addSubject,
        isFrontDisplay: this.value,
        id: this.id
      }
      // 这个是进行添加
      if (this.addOrModification) {
        if (this.addSubject === null) {
          return this.$message('学科名称不能为空')
        }
        await add(data)
        // 这下面是修改
      } else {
        if (this.addSubject === null) {
          return this.$message('学科名称不能为空')
        }
        await update(data)
      }
      this.get()
      this.dialogVisible = false
      this.addSubject = null
    },
    // 按钮搜索输入的内容
    async search() {
      this.transience = this.subjectNames
      this.pages.page = 1
      this.get()
    },
    // 按钮清除搜索输入框
    claer() {
      this.transience = null
      this.subjectNames = null
      this.get()
    },
    // 删除
    del(data) {
      this.$confirm('您确定要删除吗').then(async () => {
        await remove(data)
        this.get()
      })
    },
    // 去标签
    goTags(id) {
      this.$router.push(`/subjects/tags/${id}`)
    },
    // 去目录
    goDirectorys(id) {
      this.$router.push(`/subjects/directorys/${id}`)
    },
    // 每页显示条数显示
    changeSize(newSize) {
      this.pages.pagesize = newSize
      this.get()
    },
    // 页码变化
    changPage(newPage) {
      this.pages.page = newPage
      this.get()
    },
    // 对话框
    handleClose(done) {
      this.$confirm('确认关闭？')
        .then(_ => {
          done()
        })
        .catch(_ => {})
    },
    // 渲染页面
    async get() {
      let res = await list({ ...this.pages, subjectName: this.transience })
      this.pages.counts = res.data.counts
      this.subjeictList = res.data.items
    },
    formatterShow(row, column, cellValue, index) {
      return cellValue === 1 ? '是' : '否'
    }
  },
  created() {
    this.get()
  }
}
</script>

<style scoped>
.search {
  margin: 10px 0;
}
.operationBtn {
  position: absolute;
  right: 30px;
}
.input {
  width: 400px;
}
.block {
  margin: 20px 0;
  text-align: center;
}
</style>
