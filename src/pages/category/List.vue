<template>
  <div class="arttcle">
    <!-- 按钮 -->
    <div class="btns">
      <el-button type="primary" size="small" @click="toAdd">新增</el-button>
      <el-button type="danger" size="small" @click="toBatchDenete">批量删除</el-button>
    </div>
    <!-- 表格 -->
    <el-table :data="categorys" style="width: 100%" @selection-change="handleSelectionChange">
      <el-table-column type="selection" width="55"></el-table-column>
      <el-table-column prop="id" label="编号" width="180"></el-table-column>
      <el-table-column prop="name" label="栏目名称" width="180"></el-table-column>
      <el-table-column prop="description" label="栏目描述"></el-table-column>
      <el-table-column prop="category.name" label="父栏目"></el-table-column>
      <el-table-column fixed="right" label="操作" align="center" width="150">
        <template slot-scope="scope">
          <el-button @click="toDelete(scope.row.id)" type="text" size="small">删除</el-button>
          <el-button @click="toEdit(scope.row)" type="text" size="small">编辑</el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 模态框 -->
    <el-dialog title="新增栏目信息" :visible.sync="dialogFormVisible">
      <el-form :model="form">
        <el-form-item label="栏目名称" label-width="80px">
          <el-input v-model="form.name" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="所属栏目" label-width="80px">
            <el-select clearable="" v-model="form.parentId" placeholder="请选择所属栏目">
            <el-option v-for="c in categorys" :key="c.id" :label="c.name" :value="c.id"></el-option>
            </el-select>
        </el-form-item>
        <el-form-item label="栏目描述" label-width="80px">
          <el-input type="textarea" rows="5" v-model="form.description" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button size="small" @click="dialogFormVisible = false">取 消</el-button>
        <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
      </div>
    </el-dialog>
    <!-- 模态框 -->
  </div>
</template>
<script>
import request from "@/utils/request";
export default {
  //提供数据
  data() {
    return {
      dialogFormVisible: false,
      form: {},
      categorys: [],
      ids: []
    };
  },
  // 生命周期
  created() {
    this.reloadData();
  },
  //方法，事件处理函数
  methods: {
    handleSelectionChange(val) {
      this.ids = val.map(item => item.id);
    },
    //批量删除
    toBatchDenete() {
      this.$confirm("此操作将永久删除选中栏目, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        let url = "/comment/deleteById";
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
    submitHandler() {
      let url = "/category/saveOrUpdate";
      request.post(url, this.form).then(response => {
        //提示
        // 通知
        this.$message({
          message: response.message,
          type: "success"
        });
        //关闭模态框
        this.dialogFormVisible = false;
        //重新载入
        this.reloadData();
      });
    },
    reloadData() {
      //调用ajax查询文章
      request.get("/category/cacadeFindAll").then(result => {
        this.categorys = result.data;
      });
    },
    toEdit(record) {
      //跳转到编辑页面
      this.dialogFormVisible = true;
      //回显数据 给form赋值
      this.form = record;
    },
    toDelete(id) {
      this.$confirm("此操作将永久删除该栏目, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        // 交互
        let url = "/category/deleteById";
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
    toAdd() {
      this.form = {};
      //跳转到编辑页面
      this.dialogFormVisible = true;
    }
  }
};
</script>
<style scoped>
</style>