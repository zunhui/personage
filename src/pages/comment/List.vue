<template>
  <div class="arttcle">
    <!-- 按钮 -->
    <div class="btns">
      <el-button type="danger" size="small" @click="toBatchDenete">批量删除</el-button>
    </div>
    <!-- 表格 -->
    <el-table :data="comments" style="width: 100%" @selection-change="handleSelectionChange">
      <el-table-column type="selection" width="55"></el-table-column>
      <el-table-column prop="id" label="编号" ></el-table-column>
      <el-table-column prop="username" label="用户名"></el-table-column>
      <el-table-column prop="articleExtend.title" label="文章标题"></el-table-column>
      <el-table-column prop="articleExtend.category.name" label="所属栏目"></el-table-column>
      <el-table-column prop="content" label="评论内容"></el-table-column>
      <el-table-column prop="status" label="状态"></el-table-column>
      <el-table-column fixed="right" label="操作" align="center" width="150">
        <template slot-scope="scope">
          <el-button @click="toDelete(scope.row.id)" type="warning" size="small">删除</el-button>
          <el-button @click="toEdit(scope.row.id)" type="primary" size="small">审核</el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 模态框 -->   
    <el-dialog title="提示" :visible.sync="centerDialogVisible" width="30%" center>
        <div style="text-align:center">
         <el-form ref="form" :model="form" label-width="80px">
            <el-form-item label="审核状态">
                <el-radio-group v-model="form.status">
                <el-radio label="审核通过"></el-radio>
                <el-radio label="审核不通过"></el-radio>
                </el-radio-group>
            </el-form-item>
                <el-form-item>
                    <el-button type="primary" @click="onSubmit">确认</el-button>
                    <el-button @click="centerDialogVisible=false">取消</el-button>
                </el-form-item>
            </el-form>
        </div> 
    </el-dialog>
  </div>
</template>
<script>
import request from "@/utils/request";
export default {
  //提供数据
  data() {
    return {
      centerDialogVisible: false,
      form: {},
      comments: [],
      ids: []
    };
  },
  // 生命周期
  created() {
    this.reloadData();
  },
  //方法，事件处理函数
  methods: {
      //批量删除
    handleSelectionChange(val) {
      this.ids = val.map(item => item.id);
    },
    onSubmit(){
        this.centerDialogVisible=false,
        request.post("/comment/auditById",this.form).then(response=>{
            this.$message({
            message: response.message,
            type: "success"
            });
            this.reloadData();
        })
    },
    //批量删除
    toBatchDenete() {
      this.$confirm("此操作将永久删除选中用户, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        // 交互
        let url = "/comment/batchDeleteById";
        request.post(url, this.ids).then(response => {
          this.$message({
            message: response.message,
            type: "success"
          });
          //重新载入
          this.reloadData();
        });
      });
    },
    reloadData() {
      //调用ajax查询文章
      request.get("/comment/findAll").then(result => {
        this.comments = result.data;
      });
    },
    toEdit(id) {
      //跳转到编辑页面
      this.centerDialogVisible = true;
      let url = "/comment/selectById?id="+id;
        request.get(url).then(result => {   
            this.form = result.data;
          });
    },
    toDelete(id) {
      this.$confirm("此操作将永久删除该用户, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        // 交互
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
  }
};
</script>
<style scoped>
    
</style>