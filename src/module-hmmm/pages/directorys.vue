<template>
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
      <el-button type="info"  @click="dialogVisible = true">新建目录</el-button>
      <!-- 弹出框 -->
        <el-dialog title="新建目录" :visible.sync="dialogVisible" width="40%" :before-close="handleClose">
      <el-form>
        <el-form-item label="目录名称">
          <el-input placeholder="请输入" v-model="formData.directoryName"></el-input>
        </el-form-item>
         <el-form-item label="目录id">
          <el-input placeholder="请输入" v-model="formData.subjectID"></el-input>
        </el-form-item>
      </el-form>
     <span slot="footer" class="dialog-footer">
    <el-button @click="dialogVisible = false">取 消</el-button>
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
        <el-option v-for="item in options"
      :key="item.value"
      :label="item.label"
      :value="item.value"></el-option>
      </el-select>
      <el-button class="margin">清除</el-button>
      <el-button type="primary" class="margin">搜索</el-button>
    </el-form-item>
    <!-- 表格 -->
    <el-form-item>
      <el-table border :data="tableData" style="width:1112px" >
        <el-table-column prop="id" label="序号" width="70"></el-table-column>
        <el-table-column prop="directoryName" label="目录名称" width="160"></el-table-column>
        <el-table-column prop="creatorID" label="创建者" width="160"></el-table-column>
        <el-table-column prop="addDate" label="创建日期" width="200"></el-table-column>
        <el-table-column prop="totals" label="面试题数量" width="160"></el-table-column>
        <el-table-column prop="state" label="状态" width="160"></el-table-column>
        <el-table-column label="操作" width="200">
          <template slot-scope="scope">
            <el-button type="text" size="small">修改</el-button>
            <el-button type="text" size="small">禁用</el-button>
            <el-button type="text" size="small" @click="delDir(scope.row)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
    </el-form-item>
    <!-- 分页 -->
    <el-form-item>
      <el-pagination background layout="prev, pager, next" :total="1000" class="paging">
      </el-pagination>
    </el-form-item>
  </el-form>
</template>

<script>
import { list, remove, add } from '@/api/hmmm/directorys'

export default {
  name: 'DirectorysList',
  data() {
    return {
      formData: {
          // 新建目录的ID
      subjectID: 1,
      // 新建目录的名
      directoryName: ''
      },
    
      dialogVisible: false,
      tableData: [],
      // 搜索目录名称
      dirname: '',
      options: [
        {value: '0', label: '禁用'},
        {value: '1', label: '启用'}
      ],
      value: []
    }
  },
  methods: {
    // 删除列表的某条数据
    delDir (info) {
    //  console.log(id)
     this.$confirm('想好再点别后悔', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
     }).then(async () => {
       await remove(info)
       this.getData() // 获取列表数据
     }).catch(() => {
       this.$message({
         type: 'info',
         message: '已取消删除'
       })
     })
     
  },
    // 获取列表数据封装函数
   async getData() {
      var subject = await list()
      this.tableData = subject.data.items

    },
    // 添加目录
   async addDir() {
      await add(this.formData)
      this.formData.directoryName = ''
      this.dialogVisible = false
      this.getData() // 获取列表数据
      
  },
    // 返回学科
    getback() {
      this.$router.push('/subjects/list')
    },
    //  弹框
      handleClose (done) {
        this.$confirm('确认关闭？')
        .then(_ => {
         done()
        })
        .catch(_ => {})
      }
    
  },
  created() {
    this.getData() // 获取列表数据
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
.paging{
  margin-left: 720px;
}
</style>
