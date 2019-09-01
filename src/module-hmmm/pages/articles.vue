<template>
  <div class="dashboard-container">
    <el-container>
      <el-header height="100px">
        <el-button
          type="primary"
          @click="addSkill"
          icon="el-icon-position"
          style="margin-top:5px"
        >新增面试技巧</el-button>
        <el-form :model="formData">
          <el-row :gutter="20">
            <el-col :span="5" type="flex">
              <el-form-item class="grid-content bg-purple" props="keywords">
                <el-input  placeholder="请输入题目编号/题干" v-model="formData.keywords" ></el-input>
              </el-form-item>
            </el-col>
            <el-col :span="15">
              <div class="grid-content bg-purple">
                <el-row>
                  城市
                  <el-select
                    v-model="searchForm.province"
                    @change="getProvince(searchForm.province)"
                  >
                    <el-option v-for="item in provinceList" :key="item" :value="item" :label="item"></el-option>
                  </el-select>地区
                  <el-select v-model="searchForm.city">
                    <el-option v-for="item in cityList" :label="item" :key="item" :value="item"></el-option>
                  </el-select>
                </el-row>
              </div>
            </el-col>
            <el-col :span="4">
              <div class="grid-content bg-purple" style="display:flex">
                <el-button type="text" @click="resetFields">清除</el-button>
                <el-button type="primary" @click="searchKeyWord">搜索</el-button>
                
              </div>
            </el-col>
          </el-row>
        </el-form>
      </el-header>
      <el-main>
        <el-table :data="ArticlesList" border style="width: 100%" v-loading="loading">
          <el-table-column prop="id" sortable=true label="序号" width="150" ></el-table-column>
          <el-table-column prop="title" label="标题" width="300" ></el-table-column>
          <el-table-column prop="visits" label="阅读数" width="150" ></el-table-column>
          <el-table-column 
            prop="state"
            label="状态"
            width="150"
            :formatter="formatter"
          ></el-table-column>
          <el-table-column prop="creator" label="录入人" width="150" ></el-table-column>
          <el-table-column label="操作" width="300">
            <template slot-scope="scope">
              <el-button @click="handleClick(scope.row)"  type="text" size="small">预览</el-button>
              <el-button type="text" size="small" @click="updataArticle(scope.row)">修改</el-button>
              <el-button type="text" size="small" @click="deleteItem(scope.row)">删除</el-button>
              <el-button type="text" size="small" @click="disableItem(scope.row)">禁用</el-button>
            </template>
          </el-table-column>
        </el-table>
        <el-row style="margin-top:10px">
          <el-pagination
            @size-change="handleSizeChange"
            :current-page="countList.page"
            :page-size="countList.pagesize"
            layout="total, sizes, prev, pager, next, jumper"
            :total="countList.counts"
            @current-change="handleCurrentChange"
          ></el-pagination>
        </el-row>
      </el-main>
    </el-container>
  </div>
</template>

<script>
import { list, remove, detail, update, state } from '@/api/hmmm/articles' // 获取文章信息
import { provinces, citys } from '@/api/hmmm/citys' // 获取省份信息

export default {
  name: 'ArticlesList',
  data() {
    return {
      
      loading: false,
      formData: {
        keywords: ''
      },
      searchForm: {
        questionId: '',
        province: '',
        city: ''
      },
      countList: {
        counts: 0, // 总条数
        pagesize: 10, // 每页显示条数
        page: 1 // 默认当前页码
      },
      updateList: {
        id: '',
        title: '',
        articleBody: '',
        videoURL: ''
      },
      // 分页列表
      provinceList: [], // 省份列表
      cityList: [], // 城市列表
      ArticlesList: [] // 文章信息列表
    }
  },
  methods: {
    // 封装请求数据信息
    async getDataList() {
      let getArticles = await list(this.countList) // 请求列表数据
      this.ArticlesList = getArticles.data.items // 获取文章数据
      this.countList.counts = getArticles.data.counts
      // this.countList = getArticles.data // 获取分页数据
      this.loading = false
    },
    // 清除关键字输入框内容
    resetFields() { 
      this.formData.keywords = ''
      this.searchForm.province = ''
      this.searchForm.city = ''
      this.$message('已清除数据', 'warning')
    },
    // 搜索关键字
    searchKeyWord() {
      console.log(this.formData.keywords)

    },
    // 预览数据
    async handleClick(row) {
      let listInfo = await detail(row)
      console.log(listInfo)
    },
    // 修改信息
   async updataArticle(row) {
      let updataInfo = await update(row)
      // console.log(updataInfo)
      this.$router.push(`./list/addskills/${row.id}`)
    },
    // 禁用Item
    async disableItem(row) {
      await state({ id: row.id, state: Number(!row.state) })

      this.getDataList()
    },
    // 删除item
    async deleteItem(row) {
      let confirm = await this.$confirm('你确定要删除吗?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).catch(() => {
        this.$message('已取消')
      })
      if (confirm) {
        let list = remove(row)
        this.getDataList()
        this.loading = false
      }
    },
    getProvince(cityName) {
      this.cityList = citys(cityName)
    },
    handleSizeChange() {},
    // 跳转页面 刷新   页数跳转
    handleCurrentChange(newPage) {
      this.loading = true
      this.countList.page = newPage
      this.getDataList()
      this.loading = false
    },
    formatter (row, column, cellValue, index) {
      return cellValue ? '启用' : '禁用'
      
    },
    addSkill() {
      this.$router.push('./list/addskills')
    }
  },
  created() {
    this.loading = true
    this.getDataList()
    this.provinceList = provinces() // 获取城市信息
  }
}
</script>

<style scoped lang='less'>
.el-header {
  color: #333;
  text-align: left;
}
.grid-content {
  margin-top: 7px;
}
.el-main {
  background-color: #e9eef3;
  color: #333;
  text-align: center;
}

.el-row {
  margin-bottom: 20px;
  &:last-child {
    margin-bottom: 0;
  }
}
.el-col {
  border-radius: 4px;
}

.grid-content {
  border-radius: 4px;
  min-height: 36px;
}
.row-bg {
  padding: 10px 0;
  background-color: #f9fafc;
}
</style>
