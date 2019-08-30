<template>
  <el-form
    style="background:white"
    :model="formData"
    status-icon
    :rules="rules"
    ref="addForm"
    label-width="100px"
    class="demo-ruleForm"
  >
    <el-form-item label="标题" prop="title">
      <el-input type="text" v-model="formData.title" placeholder="请输入文章标题" style="width:800px"></el-input>
    </el-form-item>
    <el-form-item label="文章正文" prop="articleBody">
      <quill-editor
        v-model="formData.articleBody"
        autocomplete="off"
        style="height:230px;margin-bottom:100px;width:800px"
      ></quill-editor>
    </el-form-item>
    <el-form-item label="视频地址" prop="videoURL">
      <el-input placeholder="请输入视频地址" v-model="formData.videoURL" style="width:800px"></el-input>
    </el-form-item>
    <el-form-item>
      <el-button type="primary" @click="submitInfo">提交</el-button>
      <el-button>取消</el-button>
    </el-form-item>
  </el-form>
</template>

<script>
import { add } from '@/api/hmmm/articles'
import 'quill/dist/quill.core.css' // 使用quill富文本
import 'quill/dist/quill.snow.css'
import 'quill/dist/quill.bubble.css'
import { all, async } from 'q'
export default {
  data() {
    return {
      formData: {
        title: '',
        articleBody: '',
        videoURL: ''
      },
      rules: {
        title: [
          { required: true, message: '标题不能为空', trigger: 'blur' },
          {
            min: 2,
            max: 10,
            message: '输入标题在2-10直接'
          }
        ],
        articleBody: [
          { required: true, message: '输入内容不能为空', trigger: 'blur' },
          {
            min: 2,
            max: 30,
            message: '文章内容在2-50之间'
          }
        ],
        videoURL: [
          { required: true, message: '输入视频地址不能为空', trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    // 提交新增技巧信息
    submitInfo() {
      this.$refs.addForm.validate(async isOk => {
        if (isOk) {
          await add(this.formData)
          this.$router.push('/articles/list')
        }
      })
    }
  },
  async created() {}
}
</script>

<style>
</style>
