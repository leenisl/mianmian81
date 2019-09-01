<template>
  <div class="dashboard-container">
    <div class="app-container">
      <!-- 精选题库管理 -->
      <el-card>
        <el-row class="mb20">
          <el-button type="primary" size="small" @click="goto">新增试题</el-button>
          <el-button type="primary" size="small">批量导入</el-button>
        </el-row>
        <el-form :inline="true" :model="formData" ref="form">
          <el-row type="flex" justify="space-between">
            <el-form-item label="学科" prop="subjectID">
              <el-select placeholder="请选择" v-model="formData.subjectID">
                <el-option
                  v-for="item in subjectlist"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="难度" prop="difficulty">
              <el-select placeholder="请选择" v-model="formData.difficulty">
                <el-option
                  v-for="item in difficulty"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="试题类型" prop="questionType">
              <el-select placeholder="请选择" v-model="formData.questionType">
                <el-option
                  v-for="item in questionType"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="标签" prop="tags">
              <el-select placeholder="请选择" v-model="formData.tags">
                <el-option
                  v-for="item in tagsimplelist"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="城市" prop="province">
              <el-select placeholder="请选择" v-model="formData.province">
                <el-option
                  v-for="(item,index) in capitallist"
                  :key="index"
                  :label="item"
                  :value="item"
                ></el-option>
              </el-select>
              <el-select
                collapse-tags
                style="margin-left: 20px;"
                placeholder="请选择"
                v-model="formData.city"
              >
                <el-option
                  v-for="(item,index) in cityslist"
                  :key="index"
                  :label="item"
                  :value="item"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-row>
          <el-row type="flex" justify="space-between">
            <el-form-item label="关键字" prop="keyword">
              <el-input class="wid" placeholder="请输入题目编号/题干" v-model="formData.keyword"></el-input>
            </el-form-item>
            <el-form-item label="题目备注" prop="remarks">
              <el-input class="wid" placeholder="请输入" v-model="formData.remarks"></el-input>
            </el-form-item>
            <el-form-item label="企业简称" prop="shortName">
              <el-input class="wid" placeholder="请输入" v-model="formData.shortName"></el-input>
            </el-form-item>
            <el-form-item label="方向" prop="direction">
              <el-select placeholder="请选择" class="wid" v-model="formData.direction">
                <el-option
                  v-for="(item,index) in direction"
                  :key="index"
                  :label="item"
                  :value="item"
                ></el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="录入人" prop="creatorID">
              <el-select placeholder="请选择" class="wid" v-model="formData.creatorID">
                <el-option
                  v-for="item in creatorlist"
                  :key="item.id"
                  :label="item.username"
                  :value="item.username"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-row>
          <el-row type="flex" justify="space-between">
            <el-form-item label="二级目录" prop="catalogID">
              <el-input placeholder="请输入二级目录" v-model="formData.catalogID"></el-input>
            </el-form-item>
            <div class="mr10">
              <el-button
                type="primary"
                size="small"
                @click="$refs.form.resetFields();formData.city=null"
              >清除</el-button>
              <el-button type="primary" size="small" @click="search">搜索</el-button>
            </div>
          </el-row>
        </el-form>
        <!-- 表格 -->
        <el-table :data="quslist" style="width: 100%" border>
          <el-table-column prop="id" label="序号" width="80" sortable></el-table-column>
          <el-table-column prop="number" label="试题编号" width="220"></el-table-column>
          <el-table-column prop="subjectID" label="学科" width="90" :formatter="famtsubjectID"></el-table-column>
          <el-table-column prop="questionType" label="题型" width="90" :formatter="famtquestionType"></el-table-column>
          <el-table-column prop="question" label="题干" width="250"></el-table-column>
          <el-table-column label="录入时间" width="170">
            <span slot-scope="sta">{{sta.row.addDate | parseTimeByString('{y}-{m}-{d}')}}</span>
          </el-table-column>
          <el-table-column prop="creator" label="录入人" width="120"></el-table-column>
          <el-table-column prop="difficulty" label="难度" width="90" :formatter="famtdifficulty"></el-table-column>
          <el-table-column prop="id" label="使用次数" width="90"></el-table-column>
           <el-table-column prop="chkState" width="120" :formatter="famtchkState" label="审核状态"></el-table-column>
            <el-table-column prop="chkRemarks" width="120" label="审核意见"></el-table-column>
             <el-table-column prop="publishState" width="120" label="发布状态"></el-table-column>
          <el-table-column prop="id" fixed='right' label="操作">
            <template slot-scope="obj">
        
              <el-link :underline="false" type="primary">审核</el-link>
              <el-link :underline="false" type="primary" slot="reference" @click="preview(obj.row.id)">预览</el-link>
              <el-link :underline="false" type="primary" slot="reference" @click="$router.push(`/questions/revisionQuestion/${obj.row.id}`)">修改</el-link>
              <el-link :underline="false" type="primary" slot="reference" @click="del(obj.row.id)">删除</el-link>
              <el-link :underline="false" type="primary" slot="reference" @click="alert('未开发')">下架</el-link>
            
            </template>
          </el-table-column>
        </el-table>
        <el-row type="flex" justify="center" class="mt20">
          <el-pagination
            background
            layout="prev, pager, next,sizes, jumper"
            :current-page="page.page"
            :page-size="page.pagesize"
            :total="page.total"
            :page-sizes="[1, 2, 3, 4]"
            @current-change="currentChange"
            @size-change="sizechange"
          ></el-pagination>
        </el-row>
        <el-dialog title="题目预览" :visible.sync="dialogVisible" width="50%">
          <question-dialog :id="sendId"  />
          <!-- <span slot="footer" class="dialog-footer">
            <el-button @click="dialogVisible = false">关闭</el-button>
          </span> -->
        </el-dialog>
      </el-card>
    </div>
  </div>
</template>

<script>
import { createAPI } from '@/utils/request'
import { simple } from '@/api/hmmm/subjects'
import { simple as tagsimple } from '@/api/hmmm/tags'
import { difficulty, questionType, direction, chkType } from '@/api/hmmm/constants'
import { provinces, citys } from '@/api/hmmm/citys'
import { choice as list, remove } from '@/api/hmmm/questions'
import { constants } from 'fs'
import { simple as ScreatorID } from '@/api/base/users'
import { async } from 'q'
import questionDialog from '@/module-hmmm/components/questions-preview.vue'
export default {
  name: 'QuestionsChoice',
  data() {
    return {
      sendId: '',
      dialogVisible: false,
      formData: {
        subjectID: null,
        difficulty: null,
        chkState: null,
        questionType: null,
        tags: null,
        province: null,
        city: null,
        keyword: null,
        remarks: null,
        shortName: null,
        direction: null,
        creatorID: null,
        catalogID: null
      },
      title: '试题预览',
      page: {
        page: 1,
        pagesize: 7,
        total: 0
      },
      formsearch: {},
      subjectlist: [],
      chkType,
      difficulty,
      questionType,
      direction,
      tagsimplelist: [],
      capitallist: [],
      cityslist: [],
      creatorlist: [],
      quslist: []
    }
  },
  watch: {
    'formData.province': function(newval) {
      this.cityslist = citys(newval)
    }
    // formData: {
    //   handler: async function (val, oldVal) {
    //     this.page.page = 1
    //     this.cityslist = citys(val.province)
    //     let c = await list({...this.page, ...this.formData})
    //     this.quslist = c.data.items
    //    },
    //   deep: true
    // }
  },
  methods: {
    preview(id) {
      this.dialogVisible = true
      this.sendId = id
    },
    async del(id) {
      await this.$confirm('您确定要删除该条数据?', '提示')
      await createAPI(`/questions/${id}`, 'delete')
      this.getlist({...this.page, ...this.formsearch})
    },
    goto() {
      this.$router.push('/questions/new')
    },
    async search() {
      this.page.page = 1
      for (var key in this.formData) {
        this.formsearch[key] = this.formData[key]
      }
      this.getlist({...this.formsearch, ...this.page})
    },
    async sizechange(size) {
      this.page.page = 1
      this.page.pagesize = size
      this.getlist({...this.page, ...this.formsearch})
    },
    currentChange(page) {
      this.page.page = page
      this.getlist({...this.page, ...this.formsearch})

    },
    async getlist(data) {
      let c = await list(data)
      this.page.total = c.data.pages
      this.quslist = c.data.items
    },
    famtsubjectID(row, column, cellValue, index) {
      let b = this.subjectlist.find(item => {
        if (item.value === cellValue) {
          return true
        }
      })
      return b.label
    },
    famtquestionType(row, column, cellValue, index) {
      let c = this.questionType.find(item => {
        if (item.value === Number.parseInt(cellValue)) {
          return true
        }
      })
      return c.label
    },
    famtdifficulty(row, column, cellValue, index) {
      let d = this.difficulty.find(item => {
        if (item.value === Number.parseInt(cellValue)) {
          return true
        }
      })
      return d.label
    },
    famtchkState(row, column, cellValue, index) {
    
      let e = this.chkType.find((item, index) => {
        // console.log(item, index, cellValue)
        if (item.value === Number.parseInt(cellValue)) {
          console.log('徐', item.label, cellValue)
          return true

        }
      })
      return e.label
    }
   
  },
  
  async created() {
    let simpleSubject = await simple()
    this.subjectlist = simpleSubject.data
    let tagSimple = await tagsimple()
    this.tagsimplelist = tagSimple.data
    this.capitallist = await provinces()
    this.getlist({ pagesize: 7 })
    let c = await ScreatorID()
    this.creatorlist = c.data
  },
  components: {
    questionDialog
  }
}
</script>

<style scoped>
a {
  color: #409eff;
  margin-right: 10px;
}
.mb20 {
  margin-bottom: 20px;
}
.mr10 {
  margin-right: 10px;
}
.mt20 {
  margin-top: 20px;
}
.wid {
  width: 250px;
}
.fl {
  float: left;
}
.btn {
 
  /* display: flex; */

  margin: 0 !important;
  z-index: 1;
}
.btn1 {
  
  width: 50px;
  height: 30px;
}
</style>
