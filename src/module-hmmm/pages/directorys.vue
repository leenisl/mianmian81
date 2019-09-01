<template>
  <el-card>
    <el-form class="breadcrumb">
      <!-- 面包屑 -->
      <el-form-item>
        <el-breadcrumb>
          <el-breadcrumb-item :to="{path:'/'}">学科管理</el-breadcrumb-item>
          <el-breadcrumb-item>javaEE</el-breadcrumb-item>
          <el-breadcrumb-item>目录管理</el-breadcrumb-item>
        </el-breadcrumb>
      </el-form-item>
      <!-- 两个按钮 -->
      <el-form-item>
        <el-button type="info" @click="dialogVisible = true, put = true">新建目录</el-button>

        <!-- 弹出框 -->
        <el-dialog
          :title="this.put ? '新建目录' : '修改目录'"
          :visible.sync="dialogVisible"
          width="40%"
          :before-close="handleClose"
        >
          <el-form>
            <el-form-item label="目录名称">
              <el-input placeholder="请输入" v-model="formData.directoryName"></el-input>
            </el-form-item>
          </el-form>
          <span slot="footer" class="dialog-footer">
            <el-button @click="dialogVisible = false,formData.directoryName = ''">取 消</el-button>
            <el-button type="primary" @click="addDir">确定</el-button>
          </span>
        </el-dialog>

        <el-button type="info" @click="getback">返回学科</el-button>
      </el-form-item>
      <!-- 输入框 -->
      <el-form-item>
        <span class="bgc">目录名称</span>
        <el-input placeholder="请输入" style="width:220px;" v-model="dirname"></el-input>
        <span class="bgc margin">状态</span>
        <el-select style="width:180px" v-model="value">
          <el-option
            v-for="item in options"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          ></el-option>
        </el-select>
        <el-button class="margin" @click="clearData">清除</el-button>
        <el-button type="primary" class="margin" @click="filtrate">搜索</el-button>
      </el-form-item>
      <!-- 表格 -->
      <el-form-item>
        <el-table border :data="tableData" style="width:1112px">
          <el-table-column prop="id" label="序号" width="70">
            <template slot-scope="abcd">{{abcd.row.id}}</template>
          </el-table-column>
          <el-table-column prop="directoryName" label="目录名称" width="160"></el-table-column>
          <el-table-column prop="username" label="创建者" width="160"></el-table-column>
          <el-table-column prop="addDate" label="创建日期" width="200">
            <span slot-scope="stData">{{stData.row.addDate | parseTimeByString}}</span>
          </el-table-column>
          <el-table-column prop="totals" label="面试题数量" width="160"></el-table-column>
          <el-table-column prop="state" label="状态" width="160">
            <span slot-scope="states">{{states.row.state | filterstatus}}</span>
          </el-table-column>
          <el-table-column label="操作" width="200">
            <template slot-scope="scope">
              <el-button type="text" size="small" @click="alert(scope.row)">修改</el-button>
              <el-button
                type="text"
                size="small"
                @click="forbidden(scope.row)"
              >{{scope.row.state ? "禁用" : '启动'}}</el-button>
              <el-button type="text" size="small" @click="delDir(scope.row)">删除</el-button>
            </template>
          </el-table-column>
        </el-table>
      </el-form-item>
    </el-form>
    <!-- 分页 -->
    <el-row type="flex" justify="center">
      <el-pagination
        background
        :total="page.total"
        :page-size="page.pagesize"
        :current-page="page.page"
        @current-change="fenye"
      ></el-pagination>
    </el-row>
  </el-card>
</template>

<script>
import { list, remove, add, update, removeState } from '@/api/hmmm/directorys'

export default {
  name: 'DirectorysList',
  data() {
    return {
      // 分页
      page: { page: 1, pagesize: 10, total: 0 },
      formData: {
        subjectID: Number(this.$route.params.id), // 新建目录的ID
        directoryName: '', // 新建目录的名
        id: ''
      },

      put: true, // 状态值
      dialogVisible: false, // 隐藏弹框
      tableData: [],
      dirname: '', // 搜索目录名称
      options: [{ value: '0', label: '禁用' }, { value: '1', label: '启用' }],
      value: ''
    }
  },
  methods: {
    // 分页
    fenye(newpage) {
      this.page.page = newpage
      this.getData(this.page)
    },
    // 清除搜索栏数据
    clearData() {
      this.dirname = ''
      this.value = ''
      this.getData()
    },
    // 禁用
    async forbidden(info) {
      // let params = {
      //   id: info.id,
      //   state: !info.state
      // }
      await removeState({ id: info.id, state: Number(!info.state) })

      this.getData() // 获取列表数据
      // info.state = !info.state
    },
    // 筛选
    filtrate() {
      let params = {
        directoryName: this.dirname,
        state: this.value
      }
      this.getData(params) // 获取列表数据
    },
    // 删除列表的某条数据
    delDir(info) {
      //  console.log(id)
      this.$confirm('想好再点别后悔', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
        .then(async () => {
          await remove(info)
          this.getData() // 获取列表数据
        })
        .catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          })
        })
    },
    // 获取列表数据封装函数
    async getData(params) {
      var subject = await list(params)
      this.tableData = subject.data.items
      this.page.total = subject.data.counts // 后台给的总记录条数 赋值给
      // console.log(subject.data.counts)
    },
    // 点击编辑获取数据
    alert(data) {
      this.dialogVisible = true
      this.put = false
      this.formData.directoryName = data.directoryName
      this.formData.id = data.id

    },
    //  修改和编辑事件
    async addDir() {
       this.put ? await add(this.formData) : await update(this.formData)
        this.formData.directoryName = ''
        this.dialogVisible = false
        this.getData() // 获取列表数据
    },

    // 返回学科
    getback() {
      this.$router.push('/subjects/list')
    },
    //  弹框
    handleClose(done) {
      this.$confirm('确认关闭？')
        .then(_ => {
          done()
        })
        .catch(_ => {})
    }
  },
  created() {
    this.getData() // 获取列表数据
  },
  filters: {
    filterstatus(value) {
      return value ? '启动' : '禁用'
    }
  }
}
</script>

<style  scoped>
.margin {
  margin-left: 20px;
}
.bgc {
  background-color: #ccc;
  display: inline-block;
  padding: 0 8px;
  border-radius: 5px;
}
.breadcrumb {
  margin-top: 15px;
  margin-left: 15px;
}
.el-breadcrumb__inner a,
.el-breadcrumb__inner.is-link {
  color: #000;
}
.el-breadcrumb__inner.is-link {
  color: black !important;
}
.paging {
  margin-left: 720px;
}
</style>
