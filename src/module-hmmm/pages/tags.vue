<template>
  <div class="zuida">
    <el-card>
      <el-row>
        <el-button type="primary" @click="xinjian">新建标签</el-button>
        <el-button type="primary" @click="tiaoz">返回学科</el-button>
      </el-row>
      <el-row class="biao">
        标签名称:
        <el-input placeholder="请输入内容" style="width:200px"></el-input>
      </el-row>
      <el-table :data="list" border style="width: 100%">
        <el-table-column prop="id" label="序号" width="100"></el-table-column>
        <el-table-column prop="tagName" label="标签名称" width="180"></el-table-column>
        <el-table-column prop="username" label="创建者"></el-table-column>
        <el-table-column prop="addDate" label="创建日期"></el-table-column>
        <el-table-column prop="totals" label="面试题数量"></el-table-column>
        <el-table-column prop="state" label="状态" :formatter="status"></el-table-column>
        <el-table-column label="操作">
          <template slot-scope="stData">
            <el-link type="primary" @click="gai(stData.row)">修改</el-link>
            <el-link type="primary">禁用</el-link>
            <el-link type="primary" @click="shan(stData.row)">删除</el-link>
          </template>
        </el-table-column>
      </el-table>
      <el-row type="flex" justify="center">
        <el-pagination
          @current-change="qiehu"
          :current-page="pages.page"
          :page-size="pages.pagesize"
          background
          layout="prev, pager, next"
          :total="pages.count"
        ></el-pagination>
      </el-row>
      <el-dialog title="新建标签" :visible.sync="dialogVisible" width="40%">
        <span>
          *标签名称
          <el-input placeholder="请输入内容" style="width:200px" v-model="addForm.tagName"></el-input>
        </span>
        <span slot="footer" class="dialog-footer">
          <el-button @click="dialogVisible = false">取 消</el-button>
          <el-button type="primary" @click="addTags">确 定</el-button>
        </span>
      </el-dialog>
    </el-card>
  </div>
</template>

<script>
import { createAPI } from '@/utils/request'
import { list, add, remove, update } from '../../api/hmmm/tags'
export default {
  name: 'TagsList',
  data() {
    return {
      dialogVisible: false,
      list: [],
      pages: {
        pagesize: 10, // 每页多少条
        count: 0, // 总共多少条
        page: 1 // 当前页面,
      },
      // 添加功能
      addForm: {
        subjectID: 0,
        tagName: '' // 标签名称去和输入框双向绑定
      },
      flag: true,
      objid: ''
    }
  },
  methods: {
    // 返回学科跳转
    tiaoz() {
      this.$router.push('/subjects/list')
    },
    qiehu(newpage) {
      this.pages.page = newpage
      this.gitlie()
    },
    // 渲染数据
    async gitlie() {
      // 带过去
      let lieb = await list(this.pages)
      this.list = lieb.data.items
      this.pages.count = lieb.data.counts
    },
    // 状态
    status(row, column, cellValue, index) {
      return cellValue === 1 ? '启用' : '屏蔽'
    },
    xinjian() {
      this.flag = false
      this.addForm.tagName = ''
      this.dialogVisible = true
    },
    // 添加标签
     addTags() {
      if (this.flag) {
       
        update({id: this.objid}).then(res => {
              this.addForm = data
            this.gitlie()
        })
        // console.log(this.objid)
        // await createAPI(`/tags/${this.objid}`, 'put')
        // console.log(1)
      } else {
        add(this.addForm).then(res => {
        this.gitlie()
        this.dialogVisible = false
      })
      } 
   
    },
    // 删除
    shan(info) {
      this.$confirm('确定要删除吗', '提示').then(() => {
        remove(info).then(res => {
          // console.log(info)
          this.gitlie()
        })
      })
    },
    // 修改
    gai(obj) {
      this.flag = true
      // console.log(obj)
      this.addForm.tagName = obj.tagName
      this.dialogVisible = true
      this.objid = obj.id
    }
  },
  created() {
    this.gitlie()
  }
}
</script>
<style  scoped>
.biao {
  margin-top: 15px;
  margin-bottom: 20px;
}
.zuida {
  padding: 20px;
}
</style>
