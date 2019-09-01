<template>
  <div class="dashboard-container">
    <div class="app-container info-card" style="padding-bottom: 40px">
      <div>
        <div class="border">
          <el-row :gutter="20" class="mb">
            <el-col :span="12">
              <div class="grid-content bg-purple">【题库】:面试宝典题库</div>
            </el-col>
            <el-col :span="12">
              <div class="grid-content bg-purple">【学科】：{{famtsubjectID(info.subjectID)}}</div>
            </el-col>
          </el-row>
          <el-row :gutter="20" class="mb">
            <el-col :span="6">
              <div class="grid-content bg-purple">【题型】：{{info.questionType |famtquestionType}}</div>
            </el-col>
            <el-col :span="6">
              <div class="grid-content bg-purple">【编号】：{{info.number}}</div>
            </el-col>
            <el-col :span="6">
              <div class="grid-content bg-purple">【难度】：{{info.difficulty |famtdifficulty}}</div>
            </el-col>
            <el-col :span="6">
              <div class="grid-content bg-purple">【标签】：{{info.tags}}</div>
            </el-col>
          </el-row>
          <el-row :gutter="20" class="mb">
            <el-col :span="6">
              <div class="grid-content bg-purple">【目录】：{{info.catalogID}}</div>
            </el-col>
            <el-col :span="6">
              <div class="grid-content bg-purple">【企业】：{{info.enterpriseID}}</div>
            </el-col>
            <el-col :span="6">
              <div class="grid-content bg-purple">【方向】：{{info.direction}}</div>
            </el-col>
            <el-col :span="6">
              <div class="grid-content bg-purple">【城市】：{{info.city}}</div>
            </el-col>
          </el-row>
        </div>
        <div class="border">
          <el-row :gutter="20" class="mb mt20">
            <el-col>【题干】:</el-col>
          </el-row>
          <div class="pl20">
            <p>{{info.question}}</p>
            <p>单选题选项：（以下选中的选项为正确答案）</p>
            <p>
              <el-radio v-model="radio" label="1">备选项</el-radio>
            </p>
            <p>
              <el-radio v-model="radio" label="1">备选项</el-radio>
            </p>
            <p>
              <el-radio v-model="radio" label="1">备选项</el-radio>
            </p>
            <p>
              <el-radio v-model="radio" label="1">备选项</el-radio>
            </p>
          </div>
        </div>
        <div class="border">
          <el-row :gutter="20" class="mb mt20">
            <el-col>【参考答案】:</el-col>
          </el-row>
        </div>
        <div class="border">
          <el-row :gutter="20" class="mb mt20">
            <el-col>【答案解析】:</el-col>
          </el-row>
          <p class="pl20">{{info.answerID}}</p>
        </div>
        <div>
          <el-row :gutter="20" class="mb mt20">
            <el-col>【题目备注】:</el-col>
          </el-row>
          <p class="pl20">{{info.remarks}}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { simple } from '@/api/hmmm/subjects'
import { difficulty, questionType } from '@/api/hmmm/constants'
// var subjectlist = null
export default {
  name: 'QuestionsPreview',
  props: ['info', 'subjectlist'],
  data() {
    return {
      radio: ''
      // info: {
      //   subjectID: 1,
      //   questionType: 1,
      //   difficulty: 1,
      //   number: null,
      //   tags: null,
      //   catalogID: null,
      //   enterpriseID: null,
      //   direction: null,
      //   city: null,
      //   question: null,
      //   answerID: null,
      //   remarks: null
      // }
    }
  },
  methods: {
    async getInfo() {
      let a = await detail({ id: this.id, isNext: false })
      this.info = a.data
    },
    famtsubjectID(value) {
      if (value > 0) {
        let b = this.subjectlist.find(item => {
          if (Number(item.value) === Number(value)) {
            return item
          }
        })
        return b.label
      } else {
        return ''
      }

      // if (typeof b.label === 'undefined') {
      //   return ''
      // } else {
      //   return b.label
      // }
    }
      
  },
  filters: {
    
      // if (subjectlist === null) {
      //   let a = simple()
      //   console.log('a:', a)
      //   a
      //     .then(rst => {
      //       // subjectlist = rst.data
      //       console.log('rst:', rst)
      //       // let b = subjectlist.find(item => {
      //       //   if (Number(item.value) === Number(value)) {
      //       //     return item
      //       //   }
      //       // })
      //       // console.log('b:', b)
      //       // return b.label
      //     })
      //     .catch(err => {
      //       console.log('err:', err)
      //     })
      // }
      // else {
      //   let b = subjectlist.find(item => {
      //     if (item.value === value) {
      //       return item
      //     }
      //   })
      //   return b.label
      // }

      // return 'aaaaaaa'
    
    famtquestionType(value) {
      // let c = questionType.find(item => {
      //   if (item.value === Number.parseInt(value)) {
      //     return true
      //   }
      // })
      // return c.label
      if (value > 0) {
       let c = questionType.find(item => {
        if (item.value === Number.parseInt(value)) {
          return true
        }
      })
      return c.label
      } else {
        return ''
      }
    },
    famtdifficulty(value) {
      if (value > 0) {
        let d = difficulty.find(item => {
        if (item.value === Number.parseInt(value)) {
          return true
        }
      })
      return d.label
      } else {
        return ''
      }
    }
  },
  // async beforeCreate() {
  //   let a = await simple()
  //   subjectlist = a.data
  // },
  async created() {
    // this.getInfo()
  }
}
</script>

<style scoped>
.mb {
  margin-bottom: 20px;
}
.mt20 {
  margin-top: 20px;
}
.pl20 {
  padding-left: 30px;
}
.border {
  border-bottom: 1px solid #ccc;
}
.info-card {
  border: 1px solid #ccc;
  padding: 15px;
}
</style>
