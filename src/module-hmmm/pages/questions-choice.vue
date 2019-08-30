<template>
  <div class="dashboard-container">
    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <h3>精选题库</h3>
        <!-- 按钮部分 -->
        <div style="margin:8px">
          <el-button type="primary">新增试题</el-button>
          <el-button type="primary">批量加入</el-button>
        </div>
        <!-- 筛选部分form -->
        <el-form>
          <el-row type="flex" justify='space-around'>
            <el-col :span="4">
              <el-form-item label="学科">
                <el-select v-model="searchForm.Subjects" placeholder="请选择">
                  <el-option
                    v-for="item in Subjects"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value"
                  ></el-option>
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="4">
              <el-form-item label="状态">
                <el-select v-model="searchForm.status" placeholder="请选择">
                  <el-option
                    v-for="item in status"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value"
                  ></el-option>
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="4">
              <el-form-item label="难度">
                <el-select v-model="searchForm.difficulty" placeholder="请选择">
                  <el-option
                    v-for="item in difficulty"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value"
                  ></el-option>
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="试题类型">
                <el-select v-model="searchForm.questionType" placeholder="请选择">
                  <el-option
                    v-for="item in questionType"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value"
                  ></el-option>
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="4">
              <el-form-item label="标签">
                <el-input style="width:200px"  placeholder="请输入内容"   ></el-input>
              </el-form-item>
            </el-col>
            
          </el-row>
                 <el-row type="flex" justify='space-between'>
            <el-col :span="5">
              <el-form-item label="录入人">
                <el-input style="width:200px"  placeholder="请输入内容"   ></el-input>
              </el-form-item>
            </el-col>
            <el-col :span="5">
              <el-form-item label="关键字">
                <el-input style="width:200px" v-model="searchForm.pKeys" @input="searchCodes()" placeholder="请输入内容"   ></el-input>
              </el-form-item>
            </el-col>
            <el-col :span="5">
              <el-form-item label="题目备注">
                <el-input style="width:200px"  placeholder="请输入内容"   ></el-input>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="二级目录">
               <el-input style="width:200px"  placeholder="请输入内容"   ></el-input>
              </el-form-item>
            </el-col>
 </el-row>
          <el-row type="flex" justify='space-around' >
             <el-col :span="7">
              <el-form-item label="城市">
                <el-select  v-model="citysObj.provinces" @change="selectCitys()" placeholder="请选择">
                  <el-option
                    v-for="item in citys"
                    :key="item"
                    :label="item"
                    :value="item"
                  ></el-option>
                </el-select>
                 <el-select v-model="citysObj.citys" placeholder="请选择">
                  <el-option
                    v-for="item in pleace"
                    :key="item"
                    :label="item"
                    :value="item"
                  ></el-option>
                </el-select>
              </el-form-item>
            </el-col>
                  <el-col :span="6">
                    
              <el-form-item label="企业简称">
                <el-input style="width:200px"  placeholder="请输入内容"   ></el-input>
              </el-form-item>
            </el-col>
                 <el-col :span="6">
                    
              <el-form-item label="方向">
               <el-input style="width:200px"  placeholder="请输入内容"   ></el-input>
              </el-form-item>
            </el-col>
               
              </el-row>
<el-row type="flex" justify='' >
<el-button type="primary" >搜索</el-button>
<el-button type="primary" >清除</el-button>
              </el-row>
        </el-form>
      </div>
<!-- 数据列表 -->
<el-tabs v-model="activeName" type="card" @tab-click="handleClick">
    <el-tab-pane label="全部" name="all">
<el-table
 border
      :data="tableData"
      style="width: 100%">
      <el-table-column
        prop="id"
        label="编号"
        width="180">
      </el-table-column>
      <el-table-column
        prop="number"
        label="试题编号"
        width="180">
      </el-table-column>
      <el-table-column
        prop="subjectID"
        label="学科目录">
      </el-table-column>
      <el-table-column
        prop="province"
        label="企业省份">
      </el-table-column>
       <el-table-column
        prop="city"
        label="城市">
      </el-table-column>
       <el-table-column
        prop="direction"
        label="方向">
      </el-table-column>
      <el-table-column
        prop="questionType"
        label="题型">
      </el-table-column>
       <el-table-column
        prop="difficulty"
        label="难度">
      </el-table-column>
       <el-table-column
        prop="question"
        label="题干">
      </el-table-column>
       <el-table-column
        prop="videoURL"
        label="视频地址">
      </el-table-column>
        <el-table-column
        prop="answer"
        label="答案">
      </el-table-column> 
       <el-table-column
        prop="addDate"
        label="添加时间">
      </el-table-column> 
       <el-table-column
        prop="creator"
        label="录入人">
      </el-table-column> 
      <el-table-column
        prop="chkState"
        label="审核状态">
      </el-table-column> 
      <el-table-column
        prop="chkRemarks"
        label="审核意见">
      </el-table-column> 
      <el-table-column
        prop="publishState"
        label="发布状态">
      </el-table-column> 
      
    </el-table> 
     <el-row type="flex" justify="center" >
  <el-pagination
  background
  layout="prev, pager, next"
  :total="pages"
  :page-size="pager.pagesize"
  :current-page='pager.page? pager.page:1'
  @current-change='currentChange'>
</el-pagination>
</el-row>
    </el-tab-pane>
    <el-tab-pane label="未审核" name="nopass">未审核</el-tab-pane>
    <el-tab-pane label="已通过" name="pass">
      <el-table
 border
      :data="tableData"
      style="width: 100%">
      <el-table-column
        prop="id"
        type="index"
        label="编号"
        width="180">
      </el-table-column>
      <el-table-column
        prop="number"
        label="试题编号"
        width="180">
      </el-table-column>
      <el-table-column
        prop="subjectID"
        label="学科目录">
      </el-table-column>
      <el-table-column
        prop="province"
        label="企业省份">
      </el-table-column>
       <el-table-column
        prop="city"
        label="城市">
      </el-table-column>
       <el-table-column
        prop="direction"
        label="方向">
      </el-table-column>
      <el-table-column
        prop="questionType"
        label="题型">
      </el-table-column>
       <el-table-column
        prop="difficulty"
        label="难度">
      </el-table-column>
       <el-table-column
        prop="question"
        label="题干">
      </el-table-column>
       <el-table-column
        prop="videoURL"
        label="视频地址">
      </el-table-column>
        <el-table-column
        prop="answer"
        label="答案">
      </el-table-column> 
       <el-table-column
        prop="addDate"
        label="添加时间">
      </el-table-column> 
       <el-table-column
        prop="creator"
        label="录入人">
      </el-table-column> 
      <el-table-column
        prop="chkState"
        label="审核状态">
      </el-table-column> 
      <el-table-column
        prop="chkRemarks"
        label="审核意见">
      </el-table-column> 
      <el-table-column
        prop="publishState"
        label="发布状态">
      </el-table-column> 
       <el-row type="flex" justify="center" >
  <el-pagination
  background
  layout="prev, pager, next"
  :total="pages"
  :page-size="pager.pagesize"
  :current-page='pager.page? pager.page:1'
  @current-change='currentChange'>
</el-pagination>
</el-row>
    </el-table> 
    </el-tab-pane>
  </el-tabs>
      
    </el-card>
  
  </div>
</template>

<script>
import {simple as subjects} from '../../api/hmmm/subjects'
import {status, difficulty, direction, questionType} from '../../api/hmmm/constants'
import { simple as tags } from '../../api/hmmm/tags'
import {provinces, citys} from '../../api/hmmm/citys'
import {choice} from '../../api/hmmm/questions'
export default {
  name: 'QuestionsChoice',
  data() {
    return {
       activeName: 'all',
       pages: 0,
      pager: {     
        page: 1,
        pagesize: 10
      },
      options: [],
      Subjects: [],
      status: status,
      difficulty,
      direction,
      questionType,
      Tags: [],
      value: '',
      citys: '', // 省份列表
      pleace: '', // 城市列表
      citysObj: {
        provinces: '',
        citys: ''
      },
      searchForm: {
        Subjects: '',
        status: '',
        difficulty: '',
        pKeys: ''

      },
      tableData: []     
    }
  },
  methods: {
      handleClick() {
        if (this.activeName === 'pass') {
           this.getChoice({chkState: 0})
        }
      },
searchCodes() {
     
       this.getChoice({keyword: this.pKeys})
    
},

    currentChange(newPage) {
      console.log(newPage)
    this.pager.page = newPage
    this.getChoice(...this.pager)

    },
async getChoice(parmas) {
  let data = await choice(parmas)
  // console.log(data)
  this.tableData = data.data.items
  this.pages = data.data.pages
   console.log(data)
   console.log(this.pages)
},
    
    selectCitys() {
      this.pleace = citys(this.citysObj.provinces)
    },
   async getSubjects() {
      let data = await subjects()
      this.Subjects = data.data     
    }
  },  
  created() {
     this.getSubjects()
     this.citys = provinces()
      this.getChoice()    
  }
}
</script>

<style scoped>
.text {
  font-size: 14px;
}

.item {
  margin-bottom: 18px;
}

.clearfix:before,
.clearfix:after {
  display: table;
  content: '';
}
.clearfix:after {
  clear: both;
}

.box-card {
  width: 100%;

}
</style>
