<template>
  <div class="dashboard-container">
    <el-container>
      <el-container>
        <el-header height="100px">
          <el-button
            type="primary"
            @click="addSkill"
            icon="el-icon-position"
            style="margin-top:5px"
          >新增面试技巧</el-button>
          <el-row :gutter="20">
            <el-col :span="5" type="flex">
              <div class="grid-content bg-purple">
                <el-input style placeholder="请输入题目编号/题干"></el-input>
              </div>
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
                <el-button type="text" @click="centerDialogVisible = true">清除</el-button>
                <el-button type="primary">搜索</el-button>
                <el-dialog title="提示" :visible.sync="centerDialogVisible" width="30%" center>
                  <span>您确定要删除选项信息吗?</span>
                  <span slot="footer" class="dialog-footer">
                    <el-button @click="centerDialogVisible = false">取 消</el-button>
                    <el-button type="primary" @click="centerDialogVisible = false">确 定</el-button>
                  </span>
                </el-dialog>
              </div>
            </el-col>
          </el-row>
        </el-header>
        <el-main>
          <el-table :data="ArticlesList" border style="width: 100%">
            <el-table-column fixed prop="id" label="序号" width="150" :value='ArticlesList.id'></el-table-column>
            <el-table-column prop="title" label="标题" width="300" :value='ArticlesList.title'></el-table-column>
            <el-table-column prop="reads" label="阅读数" width="150" :value='ArticlesList.reads'></el-table-column>
            <el-table-column prop="state" label="状态" width="150" :value='ArticlesList.state'></el-table-column>
            <el-table-column prop="creator" label="录入人" width="150" :value='ArticlesList.creator'></el-table-column>
            <el-table-column label="操作" width="300">
              <template slot-scope="scope">
                <el-button @click="handleClick(scope.row)" type="text" size="small">预览</el-button>
                <el-button type="text" size="small">修改</el-button>
                <el-button type="text" size="small">删除</el-button>
                <el-button type="text" size="small">禁用</el-button>
              </template>
            </el-table-column>
          </el-table>
          <el-row style="margin-top:10px">
            <el-pagination
              @size-change="handleSizeChange"
              :current-page="1"
              :page-sizes="[100, 200, 300, 400]"
              :page-size="100"
              layout="total, sizes, prev, pager, next, jumper"
              :total="400"
              @current-change="handleCurrentChange"
            ></el-pagination>
          </el-row>
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>

<script>
import { list } from '@/api/hmmm/articles' // 获取文章信息
import { provinces, citys } from '@/api/hmmm/citys' // 获取省份信息
export default {
  name: 'ArticlesList',
  data() {
    return {
      centerDialogVisible: false,
      searchForm: {
        questionId: '',
        province: '',
        city: ''
      },
      // ArticlesList: [],
      provinceList: [], // 省份列表
      cityList: [], // 城市列表
      ArticlesList: [
        {
          id: '',
          count: '',
          pagesize: '',
          pages: '',
          item: ''
        }
      ]
    }
  },
  methods: {
    clearInfo() {
      alert('')
    },
    getProvince(cityName) {
      this.cityList = citys(cityName)
    },
    handleSizeChange() {},
    handleCurrentChange() {},
    addSkill() {
      this.$router.push('./list/addskills')
    }
  },

  async created() {
    let getArticles = await list()
    this.ArticlesList = getArticles.data.items
    console.log(getArticles.data.items)

    this.provinceList = provinces()
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
