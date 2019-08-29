<template>
  <el-form class="breadcrumb">
    <el-form-item>
      <el-breadcrumb separator-class="el-icon-arrow-right">
        <el-breadcrumb-item :to="{ path: '/' }" style="color:pink">学科管理</el-breadcrumb-item>
        <el-breadcrumb-item>javaEE</el-breadcrumb-item>
        <el-breadcrumb-item>目录管理</el-breadcrumb-item>
      </el-breadcrumb>
    </el-form-item>
  <el-form-item>
    <el-button type="info">新建目录</el-button>
    <el-button type="info">返回学科</el-button>
  </el-form-item>
  <el-form-item>
    <span style="">目录</span>
    <el-input placeholder="请输入"></el-input>
  </el-form-item>
  </el-form>
</template>

<script>
export default {
  name: 'DirectorysList',
  data() {
    return {}
  }
}
</script>

<style  scoped>
.breadcrumb{
  margin-top: 15px;
  margin-left: 15px;
}
</style>
