<template>
  <div class="arttcle">
    <!-- 按钮 -->
    <div class="btns">
      <el-button type="primary" size="small" @click="toPublishAritcle">发布文章</el-button>
    </div>
    <!-- 表格 -->
    <el-table :data="articles" style="width: 100%">
      <el-table-column prop="id" label="编号" width="180"></el-table-column>
      <el-table-column prop="title" label="标题" width="180"></el-table-column>
      <el-table-column prop="user.username" label="作者"></el-table-column>
      <el-table-column prop="category.name" label="所属栏目"></el-table-column>
      <el-table-column fixed="right" label="操作" align="center" width="150">
        <template slot-scope="scope">
          <el-button @click="toDelete(scope.row.id)" type="text" size="small">删除</el-button>
          <el-button @click="toReview(scope.row)" type="text" size="small">查看</el-button>
          <el-button @click="toEdit(scope.row)" type="text" size="small">编辑</el-button>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>
<script>
import request from "@/utils/request";
export default {
  //提供数据
  data() {
    return {
      articles: [],
    };
  },
  // 生命周期
  created() {
    this.reloadData();
  },
  //方法，事件处理函数
  methods: {
    reloadData() {
      //调用ajax查询文章
      request
        .get("/article/cacadeFindAll")
        .then(result => {
          this.articles = result.data;
        });
    },
    toReview(record) {
      this.$router.push({path: "/article/check"});
    },
    toEdit(record) {
      //跳转到编辑页面
      this.$router.push({ path: "/article/editor", query: record });
    },
    toDelete(id) {
      this.$confirm("此操作将永久删除该文件, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        // 交互
        let url = "/article/deleteById";
        request.get(url, { params: { id } }).then(response => {
          // 通知
          this.$message({
            message: response.message,
            type: "success"
          });
          // 重载数据
          this.reloadData();
        });
      });
    },
    toPublishAritcle() {
      //跳转到编辑页面
      this.$router.push({ path: "/article/editor" });
    }
  }
};
</script>
<style scoped>
</style>