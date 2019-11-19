<template>
  <div class="arttcle">
    <!-- 按钮 -->
    <div class="btns">
      <el-button type="danger" size="small" @click="toBatchDenete">批量删除</el-button>
    </div>
    <!-- 表格 -->
    <el-table :data="users" style="width: 100%" @selection-change="handleSelectionChange">
      <el-table-column type="selection" width="55"></el-table-column>
      <el-table-column prop="id" label="编号" ></el-table-column>
      <el-table-column prop="username" label="用户名"></el-table-column>
      <el-table-column prop="password" label="密码"></el-table-column>
      <el-table-column prop="realname" label="真实姓名"></el-table-column>
      <el-table-column prop="gender" label="性别"></el-table-column>
      <el-table-column prop="telephone" label="电话"></el-table-column>
      <el-table-column prop="status" label="状态"></el-table-column>
      <el-table-column fixed="right" label="操作" align="center" width="150">
        <template slot-scope="scope">
          <el-button @click="toDelete(scope.row.id)" type="text" size="small">删除</el-button>
          <el-button @click="toEdit(scope.row)" type="text" size="small">编辑</el-button>
          <el-button @click="toRole(scope.row)" type="text" size="small">设置</el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 模态框 -->
    <el-dialog title="修改用户信息" :visible.sync="dialogFormVisible">
      <el-form :model="form">
        <el-form-item label="用户名" label-width="80px">
          <el-input v-model="form.username" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="密码" label-width="80px">
          <el-input v-model="form.password" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="真实姓名" label-width="80px">
          <el-input v-model="form.realname" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="性别" label-width="80px">
          <el-input v-model="form.gender" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="电话" label-width="80px">
          <el-input v-model="form.telephone" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="状态" label-width="80px">
          <el-input v-model="form.status" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button size="small" @click="dialogFormVisible = false">取 消</el-button>
        <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
      </div>
    </el-dialog>
    <!-- 角色模态框 -->
    <el-dialog title="设置角色" :visible.sync="centerDialogVisible" width="30%" center>
        <div>
         <el-form ref="form" :model="user" label-width="80px">
           {{user.roles}}
            <el-form-item label="用户名">
              {{user.realname}}
            </el-form-item>
            <el-form-item label="角色" label-width="80px">
              <el-cascader v-model="user.roles" :options="roles" :props="props" clearable></el-cascader>
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
      dialogFormVisible: false,
      centerDialogVisible:false,
      form: {},
      user:[],
      users: [],
      ids: [],
      roles:[],
      props: { multiple: true ,value:'id',label:'name',emitPath:false},
    };
  },
  // 生命周期
  created() {
    this.reloadData();
    this.loadRoles();
  },
  //方法，事件处理函数
  methods: {
    loadRoles(){
      request.get("/role/findAll")
      .then(response => {
        this.roles = response.data;
      })
    },
    toRole(record){
      this.centerDialogVisible=true,
      this.user=record
    },
    handleSelectionChange(val) {
      this.ids = val.map(item => item.id);
    },
    //批量删除
    toBatchDenete() {
      this.$confirm("此操作将永久删除选中用户, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        // 交互
        let url = "/user/batchDeleteById";
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
    submitHandler() {
      let url = "/user/updateUser";
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
      request.get("user/findExtendAll").then(result => {
        this.users = result.data;
      });
    },
    toEdit(record) {
      //跳转到编辑页面
      this.dialogFormVisible = true;
      //回显数据 给form赋值
      this.form = record;
    },
    toDelete(id) {
      this.$confirm("此操作将永久删除该用户, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        // 交互
        let url = "/user/deleteById";
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