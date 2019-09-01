<template>
  <div class="zuida">
    <el-card>
      <el-row>
        <el-button type="primary" @click="xinjian">新建标签</el-button>
        <el-button type="primary" @click="tiaoz">返回学科</el-button>
      </el-row>
      <el-row class="biao">
        标签名称:
        <el-input placeholder="请输入内容" style="width:200px" @blur="xxx" v-model="list2"></el-input>
      </el-row>
      <el-table :data="list" border style="width: 100%">
        <el-table-column prop="id" label="序号" width="100"></el-table-column>
        <el-table-column prop="tagName" label="标签名称" width="180"></el-table-column>
        <el-table-column prop="username" label="创建者"></el-table-column>
        <el-table-column prop="addDate" label="创建日期">
            <span slot-scope="stData">
                  {{stData.row.addDate |parseTimeByString}}
            </span>
        </el-table-column>
        <el-table-column prop="totals" label="面试题数量"></el-table-column>
        <el-table-column prop="state" label="状态" :formatter='statu'>
         
        </el-table-column>
        <el-table-column label="操作">
          <template slot-scope="stData">
            <el-link type="primary" @click="gai(stData.row)">修改</el-link>
            <el-link type="primary" @click="statusl(stData.row)">{{stData.row.state ?'禁用':'启用'}}</el-link>
            <el-link type="primary" @click="shan(stData.row)">删除</el-link>
          </template>
        </el-table-column>
      </el-table>
    <el-row type="flex" justify="end" style="margin-top: 15px;">
    <div class="block">
      <el-pagination
      @size-change="handleSizeChange"
      @current-change="qiehu"
      :current-page="pages.page"
      :page-sizes="[10, 20, 30, 40]"
      :page-size="pages.pagesize"
      layout="total, sizes, prev, pager, next, jumper"
      :total="pages.count">
    </el-pagination>
  </div>
      </el-row>
      <el-dialog :title="!flag?'新建标签':'修改标签'" :visible.sync="dialogVisible" width="40%">
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
import { list, add, remove, update, removeState } from '../../api/hmmm/tags'
export default {
  name: 'TagsList',
  data() {
    return {
      dialogVisible: false,
      list2: '',
      list: [],
      // title: '新建标签',
      pages: {
        pagesize: 10, // 每页多少条
        count: 0, // 总共多少条
        page: 1, // 当前页面,
        subjectID: Number(this.$route.params.id)
      },
      // 添加功能
      addForm: {
        subjectID: Number(this.$route.params.id),
        tagName: '' // 标签名称去和输入框双向绑定
      },
      flag: true, // 这个控制点击是修改还是添加
      objid: ''
    }
  },
  methods: {
    // 搜索
    xxx() {
      //  let params = { tagName: this.list2 }
      // console.log(this.addForm.tagName)
      // let lieb = await list(params)
      // this.list = lieb.data.items
      this.gitlie()
    },
    statu(row, column, cellValue, index) {
       return cellValue ? '启用' : '禁用'
    },
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
      let lieb = await list({...this.pages, tagName: this.list2})
      this.list = lieb.data.items
      this.pages.count = lieb.data.counts
    },
    // 状态
  async statusl(aaa) {
      console.log(aaa)
      let xxx = {
          id: aaa.id,
          state: Number(!aaa.state)
      }
      await removeState(xxx)
      this.gitlie()
    },
    xinjian() {
      this.flag = false
      this.addForm.tagName = ''
      this.dialogVisible = true
      // let id = this.$route.params.id
      // console.log(id)
    },
    // 添加标签
     addTags() {
       // 编辑
      if (this.flag) {
             var obg = {
                id: this.objid,
                subjectID: this.addForm.subjectID,
                tagName: this.addForm.tagName
             }
        update(obg).then(res => {
              // console.log(res)
            this.gitlie()
            this.dialogVisible = false
        })
      } else {
        // 新增
        add(this.addForm).then(res => {
          this.gitlie()
          this.dialogVisible = false
        })
      } 
   
    },
    // 删除
  async  shan(info) {
     await this.$confirm('确定要删除吗', '提示')
        await remove(info)
          this.gitlie()
      
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
