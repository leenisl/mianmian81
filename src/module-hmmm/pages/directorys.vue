<template>
  <el-form class="breadcrumb">
    <el-form-item>
      <el-breadcrumb >
        <el-breadcrumb-item :to="{path:'/'}">学科管理</el-breadcrumb-item>
        <el-breadcrumb-item>javaEE</el-breadcrumb-item>
        <el-breadcrumb-item>目录管理</el-breadcrumb-item>
      </el-breadcrumb>
    </el-form-item>
  <el-form-item>
    <el-button type="info">新建目录</el-button>
    <el-button type="info">返回学科</el-button>
  </el-form-item>
  <el-form-item label="目录">
    <el-input placeholder="请输入" style="width:200px"></el-input>
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
.el-breadcrumb__inner a, .el-breadcrumb__inner.is-link{
  color: #000;
}
.el-breadcrumb__inner.is-link{
  color: black !important;
}
</style>
